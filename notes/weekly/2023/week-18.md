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
5. **Interaction.Imports.typeCheckMain**
   Takes concrete module and does type checking, adding the resulting interface to the scope monad
6. **Interaction.Imports.getInterface**
   Gets the TypeChecking.Monad.Base.Interface (cached)
7. **Interaction.Imports.createInterface**
8. **Syntax.Translation.ConcreteToAbstract.concreteToAbstract_**
   Actually transforms the top-level concrete module to abstract (TopLevelInfo)
9. **Interaction.Imports.buildInterface**
    Builds the Interface of a type checked module 