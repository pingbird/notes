---
share: true
---

# Week 18 - 2023

## Done

- Got ghcjs working

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

/home/ping/dev/ghc-wasm-meta/result/bin/cabal build --write-ghc-environment-files=always --with-compiler=/home/ping/dev/ghc-wasm-meta/result/bin/wasm32-wasi-ghc --constraint='zlib +bundled-c-zlib'
```