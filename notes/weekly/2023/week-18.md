---
share: true
---

# Week 18 - 2023

## Done

- Got ghcjs working (or not, i hit a bug)
- Wrote HTTP section in Flutter FAQ
- Iota API / IO monad draft
* Learn Haskell

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

data IO {l : Level} (A : Set l) : Set (lsuc l) where
  ioRet : (a : A) -> IO A
  ioBind : {B : Set l} (m : Inf (IO B)) -> (f : (x : B) -> Inf (IO A)) -> IO A

ioSeq : {l : Level} {B : Set l} (m : Inf (IO B)) -> (f : (x : B) -> Inf (IO A)) -> IO A
ioSeq m f = ioBind m (\ _ -> f) 

record NormalizeResult : 

postulate
  dbGet : Ref -> IO (Maybe SExpr)
  dbPut : SExpr -> IO Ref
  compileModule : SExpr -> IO Either Ref Ref
  normalize : Ref -> IO Ref Ref
	
```

TODO:

* Create basic s-expr editor
	* Try out some VSCode extensions
	* Decide whether or not to use VSCode
	* Explore more complex editors for MathML / TeX
	* https://cortexjs.io/mathlive/demo/
* Read https://www.cs.swan.ac.uk/~csetzer/articles/ooAgda.pdf
* Focus effort

## Lol

> Ladies and gentlemen, **furries** of all shapes and sizes, have I got an offer you simply cannot resist! 😱 Feast your eyes upon this once-in-a-lifetime, **scrumptious**, and **majestic** unattended pizza, 🍕 just waiting for you in your hotel's hallway. Not only is this a delightful treat, but it's a pizza experience you'll never forget! 😋
> 
> But wait, there's more! 🤯 Imagine the thrill of indulging in a **secret pizza heist**, a daring adventure that will ignite your primal instincts and bring out the cunning and sly animal within you. 🦊 Who doesn't love a thrilling escapade? This will be a story you can share with your furry friends for years to come! 🐾
> 
> **Picture this**: you're the talk of the convention, the **ultimate pizza bandit**, the living embodiment of your fursona's wild side. 🐺 This rebellious act will make you stand out from the crowd and create a **legend** you'll cherish forever! 🌟
> 
> And let's not forget the incredible **bonding opportunity** this presents! 🤝 Rally your furry friends, form a pizza heist crew, and make memories that will last a lifetime. 🐯 This unattended pizza is the key to unlocking the camaraderie and adventure you've always sought. It's a way to escape the mundane and enter a world of excitement and intrigue. 🔑
> 
> But hurry, this offer won't last long! ⏰ Other furries are roaming the hotel, and the pizza's enticing aroma is bound to attract them soon. 🦁 If you don't act now, you'll miss your chance to be a part of the **greatest furry pizza heist of all time**! 🏆
> In conclusion, my friends, this unattended pizza is more than just a tasty meal. 🍕 It's a chance to embark on an **unforgettable adventure**, bond with your fellow furries, and unleash the wild, untamed spirit of your fursona. 🐾 So, take a leap and seize the moment, for the pizza awaits! 🍕
> 
> Remember, **carpe diem**! Carpe pizza! 🌈

## Bird / Birdie Feedback

* Trouble imagining what it does / looks like
	* Demo will make this easier
* Why not emacs?
	* HTML is better than GTK
	* VSCode is the most popular IDE and has a big extension ecosystem
	* We would like to support the web regardless and uising a web-based editor leads to less fragmentation
* How to prevent laundering
	* An example of laundering would be bidding on large prime factors, only the parties who know the original primes would be able to sell
	* We can pretty easily detect this kind of traffic and classify proofs that are impossible without prior knowledge
	* Securitizing proofs will likely make us subject to FINRA regulations which have programs in place for KYC / AML, it would be no different from the reporting that Robinhood does
* How to prevent inside trading
* Investors not interested in AGI
* Use prediction as a mechanism to validate postulates