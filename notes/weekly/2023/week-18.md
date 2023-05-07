---
share: true
---

# Week 18 - 2023

## Done

- Got ghcjs working (or not, i hit a bug)
- Wrote HTTP section in Flutter FAQ
- Iota API / IO monad draft
- 

## TODO

- Practice pitch
* Learn Haskell
* Iota JS backend

## Iota Strategy

* Market is the way to go
	* Obvious revenue model
	* Immediate and long-term incentives on both sides of market
	* User retention through money making
	* Only competitors are cryptocurrencies with shallow proof solving capabilities
	* Hit up old friends for consult
* Need an impressive demo and more focused selling points
	* Good UI for interactive proof solving
	* Come up with some basic DSL grammars
	* Novel approach to referencing / embedding expressions
* Use demo to convince talent to come aboard
	* University professors
	* Advisors from major tech companies
	* Cybersecurity / formal verification labs

## The demo

1. Build Agda (with js backend) for js
2. Create basic rendering library in agda
3. Create vscode plugin that uses iota runtime to evaluate rendering library
4. Use it to implement some fancy grammars

## JS backend 

* Figure out how to seamlessly export, import, and link type checked code with Agda parser

How does Agda compile stuff?

Call chain:
1. **Main.runAgda**
   The entrypoint of the process
2. **Main.runAgdaWithOptions**
   The entrypoint but after parsing command line options
3. **Main.checkFile**
   Type checks the main file and returns the type checking scope monad
4. **Interaction.Imports.parseSource**
   Parses a source file and returns the concrete module
5. **Interaction.Imports.parseFileFromString**
   Parses a string 
6. 
7. **Interaction.Imports.typeCheckMain**
   Takes concrete module and does type checking, adding the resulting interface to the scope monad
7. **Interaction.Imports.getInterface**
   Gets the TypeChecking.Monad.Base.Interface (cached)
8. **Interaction.Imports.createInterface**
9. **Syntax.Translation.ConcreteToAbstract.concreteToAbstract_**
   Actually transforms the top-level concrete module to abstract (TopLevelInfo)
10. **Interaction.Imports.buildInterface**
    Builds the Interface of a type checked module 

Agda.Interaction.Imports.mergeInterface seems useful

TODO:

1. ~~Parse into concrete~~
	1. ~~Figure out context / monad stuff~~
	2. ~~Mock IO? Even concreteToAbstract is impure, I don't think it actually does any IO though...~~
2. Figure out why ghcjs no work
3. Try wasm backend instead
4. No hope
5. Give up and use Idris?
6. One more try...
7. Hasdfhlsdfhj HOLY SHIT it works

```
nix --extra-experimental-features "nix-command flakes" build .#all_9_6

cabal build --builddir=dist-wasm --write-ghc-environment-files=always --with-compiler=/home/ping/dev/ghc-wasm-meta/result/bin/wasm32-wasi-ghc --constraint='zlib +bundled-c-zlib'

wasmtime /home/ping/dev/iota/compiler/dist-wasm/build/wasm32-wasi/ghc-9.6.1/compiler-0.1.0.0/x/compiler/build/compiler/compiler.wasm
```

8. Get compilation working on web
	1. async IO :(
9. Get js backend working

```haskell
compileExample :: TCM ()
compileExample = do
    liftIO $ printf "setCommandLineOptions\n"

    setCommandLineOptions defaultOptions{ optAbsoluteIncludePaths = [AbsolutePath $ pack ""] }

    -- Pragmas
    let pragmaOpts = defaultPragmaOptions{ optLoadPrimitives = False, optImportSorts = False }
    stPragmaOptions `setTCLens` pragmaOpts
    liftIO $ printf "pragmaOpts: %s\n" (show pragmaOpts)
    
    let f = AbsolutePath $ pack "Main.agda"
    let rn = RawTopLevelModuleName
            { rawModuleNameRange = NoRange
            , rawModuleNameParts = NonEmpty.fromList [pack "Main"]
            }
    n <- topLevelModuleName rn
    let rf = mkRangeFile f (Just n)

    let srcStr = "{-# BUILTIN TYPE Set #-}\ndata Nat : Set where\n  zero : Nat\n  suc : Nat -> Nat"

    -- Parse string into concrete syntax tree
    ((mod, attrs), fileType) <- runPM $ parseFile moduleParser rf srcStr
    CA.checkAttributes attrs

    liftIO $ printf "mod: %s\n" (show mod)

    -- Token info
    highlighting <- generateTokenInfoFromSource rf srcStr
    stTokens `modifyTCLens` (highlighting <>)

    liftIO $ printf "highlighting: %s\n" (show highlighting)

    -- Scoping
    let topDecls = C.modDecls $ mod
    topLevelInfo <- CA.concreteToAbstract_ (CA.TopLevel f n topDecls)

    liftIO $ printf "topLevelDecls: %s\n" (show $ CA.topLevelDecls topLevelInfo)

    return ()
```

## Editor

* Node = List ℕ × List Node
	* Layer 1 - Encoding
	* Layer 2 - Metadata
	* Layer 3 - Syntax Tree

API:

```haskell
type String = List Nat
type Ref = String

record SExpr : Set where
  inductive
  field
    sId : String
    sChildren : List SExpr

indexList : {A : Set} -> List A -> Nat -> Maybe A
indexList [] _             = None
indexList (h :: _) zero    = h
indexList (_ :: t) (suc n) = indexList t n

data ListEntry {A : Set} (l : List A) : Set where
  ListEntry : (n : Nat) -> (a : A) -> indexList l n =p Just a -> ListIndex a

entries : {A : Set} (l : List A) -> List (ListEntry l)

data SLens (e : SExpr) : Set where
  sLensId : SLens e
  sLensIndex : ListEntry -> SExpr e

data IO {l : Level} (A : Set l) : Set (lsuc l) where
  ioRet : (a : A) -> IO A
  ioBind : {B : Set l} (m : Inf (IO B)) -> (f : (x : B) -> Inf (IO A)) -> IO A

ioSeq : {l : Level} {B : Set l} (m : Inf (IO B)) -> (f : (x : B) -> Inf (IO A)) -> IO A
ioSeq m f = ioBind m (\ _ -> f)

postulate
	dbGet : Ref -> IO (Maybe SExpr)
	dbPut : SExpr -> IO Ref
	compileModule : SExpr -> IO Either Ref Ref
```

TODO:

* Create basic s-expr editor
	* Try out some VSCode extensions
	* Decide whether or not to use VSCode
	* Explore more complex editors for MathML / TeX
* Read https://www.cs.swan.ac.uk/~csetzer/articles/ooAgda.pdf
