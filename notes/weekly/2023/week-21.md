---
share: true
---

# Week 21 - 2023

## Done

* Did a lot of work on Iota standard library
- Finish Iota technical design doc
- Figured out a lot of cool stuff with operator overloads using instance arguments
- Introduced Chris to Puro

## TODO

```haskell
{-# LANGUAGE BangPatterns #-}
module Main where

import Agda.Version
import Agda.Syntax.Parser
import Agda.Syntax.Position
import Agda.Syntax.TopLevelModuleName
import Agda.Utils.FileName ( AbsolutePath(AbsolutePath) )
import Data.Text hiding (null)
import qualified Data.Text.Lazy as TL
import qualified Data.List.NonEmpty as NonEmpty
import Agda.Interaction.Imports
import Text.Printf (printf)
import Agda.Compiler.Backend
import Control.Monad.IO.Class (MonadIO(..))
import Agda.TypeChecking.Warnings
    ( WhichWarnings(AllWarnings), runPM )
import Agda.Main (runTCMPrettyErrors)
import qualified Agda.Syntax.Concrete as C
import qualified Agda.Syntax.Translation.ConcreteToAbstract as CA
import Agda.Interaction.Highlighting.Generate (generateTokenInfoFromSource)
import Agda.Interaction.Library (OptionsPragma(OptionsPragma, pragmaStrings, pragmaRange))
import Agda.Syntax.Scope.Base (addModuleToScope)
import Agda.Interaction.Options (defaultOptions, CommandLineOptions (optAbsoluteIncludePaths), defaultPragmaOptions, PragmaOptions (optLoadPrimitives, optImportSorts))
import Agda.TheTypeChecker (checkDeclCached)
import Agda.TypeChecking.Errors (getAllWarnings, getAllUnsolvedWarnings)
import Agda.Interaction.Response (RemoveTokenBasedHighlighting(..))
import Agda.Utils.Monad (when)
import qualified Agda.Compiler.JS.Pretty as JSPretty
import qualified Agda.Compiler.JS.Pretty as SPretty
import Agda.Compiler.JS.Compiler (jsMod, JSOptions (optJSCompile), jsBackend)
import Agda.Compiler.Common (curMName, curIF, sortDefs, curDefs)
import qualified Agda.Compiler.JS.Syntax as JS
import qualified Agda.Compiler.JS.Compiler as JS
import Data.Maybe (catMaybes, fromMaybe, isJust)
import qualified Data.Map as Map
import qualified Data.Text.Lazy
import Agda.Interaction.FindFile (SourceFile(SourceFile))
import Agda.Utils.Lens ((^.))
import qualified Data.HashMap.Strict as HMap
import Control.Monad ((<=<))
import Agda.TypeChecking.Reduce (instantiateFull)
import Agda.Utils.Impossible (__IMPOSSIBLE__)

main :: IO ()
main = do
    printf "Hello! Agda %s\n" version
    srcStr <- readFile "Core.agda"
    runTCMPrettyErrors $ compileString "Core" srcStr
    return ()

compileString :: String -> String -> TCM ()
compileString modName srcStr = do
    setCommandLineOptions defaultOptions{ optAbsoluteIncludePaths = [AbsolutePath $ pack ""] }

    --- Pragmas
    let pragmaOpts = defaultPragmaOptions{ optLoadPrimitives = False, optImportSorts = False }
    stPragmaOptions `setTCLens` pragmaOpts
    liftIO $ printf "pragmaOpts: %s\n" (show pragmaOpts)
    
    let f = AbsolutePath $ pack $ modName ++ ".agda"
    let rn = RawTopLevelModuleName
            { rawModuleNameRange = NoRange
            , rawModuleNameParts = NonEmpty.fromList [pack modName]
            }
    n <- topLevelModuleName rn
    setTopLevelModule n
    let rf = mkRangeFile f (Just n)
    liftIO $ printf "parseFile\n"

    --- Parse string into concrete syntax tree
    ((mod, attrs), fileType) <- runPM $ parseFile moduleParser rf srcStr
    CA.checkAttributes attrs
    let source = Source
            { srcText        = Data.Text.Lazy.pack srcStr
            , srcFileType    = fileType
            , srcOrigin      = SourceFile f
            , srcModule      = mod
            , srcModuleName  = n
            , srcProjectLibs = []
            , srcAttributes  = attrs
            }

    liftIO $ printf "mod: %s\n" (show mod)

    --- Token info
    highlighting <- generateTokenInfoFromSource rf srcStr
    stTokens `modifyTCLens` (highlighting <>)
    -- liftIO $ printf "highlighting: %s\n" (show highlighting)

    --- Scoping
    let topDecls = C.modDecls $ mod
    topLevel <- CA.concreteToAbstract_ (CA.TopLevel f n topDecls)
    let decls = CA.topLevelDecls topLevel
    let scope = CA.topLevelScope topLevel
    setScope scope
    liftIO $ printf "done scoping\n"
    -- liftIO $ printf "topLevelDecls: %s\n" (show decls)

    --- Interface
    interface <- constructIScope <$> buildInterface source topLevel
    let modInfo = ModuleInfo
            { miInterface = interface
            , miWarnings = []
            , miPrimitive = False
            , miMode = ModuleTypeChecked
            }
    stCurrentModule `setTCLens'`
        Just ( iModuleName interface
            , iTopLevelModuleName interface
            )
    modifyTCLens stVisitedModules $
        Map.insert (iTopLevelModuleName interface) modInfo

    --- Type checking
    activateLoadedFileCache
    cachingStarts
    opts <- useTC stPragmaOptions
    me <- readFromCachedLog

    setTopLevelModule n
    topLevelModule <- currentTopLevelModule
    liftIO $ printf "topLevelModule: %s\n" $ show topLevelModule
    mname <- curMName
    liftIO $ printf "mname: %s\n" $ show mname

    mapM_ checkDeclCached decls `finally_` cacheCurrentLog
    warnings <- getAllWarnings AllWarnings
    unsolved <- getAllUnsolvedWarnings

    liftIO $ printf "warnings: %s\n" (show warnings)
    liftIO $ printf "unsolved: %s\n" (show unsolved)

    --- Compile to JS
    if null warnings && null unsolved then do
        let opts = JS.defaultJSOptions { optJSCompile = True }
        liftIO $ printf "coinductionKit\n"
        kit <- coinductionKit
        let moduleEnv = JS.JSModuleEnv kit True
        let backend = jsBackend

        liftIO $ printf "compileJSDef\n"
        signature <- getSignature
        let compileDefs = HMap.filter (not . defNoCompilation) (signature ^. sigDefinitions)
        let defs = Prelude.map snd $ sortDefs compileDefs
        jsDefs <- mapM (compileJSDef opts moduleEnv <=< instantiateFull) defs

        liftIO $ printf "jsMod\n"
        m <- jsMod <$> curMName

        liftIO $ printf "iImportedModules\n"
        is <- Prelude.map (jsMod . fst) . iImportedModules <$> curIF
        let es = catMaybes jsDefs
        liftIO $ printf "es: %s\n" $ show es
        let mod = JS.Module m is (JS.reorder es) Nothing

        liftIO $ printf "prettyShow\n"
        let outputJs = SPretty.prettyShow False SPretty.JSCJS mod
        liftIO $ printf "js: %s\n" outputJs
        return ()
    else pure ()

compileJSDef :: JS.JSOptions -> JS.JSModuleEnv -> Definition -> TCM (Maybe JS.Export)
compileJSDef opts env def =
  setCurrentRange (defName def) $
    JS.jsCompileDef opts env NotMain def
```