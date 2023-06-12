---
share: true
---

# Week 23 - 2023

## Done

* Working compilation of primitives, named modules, and anonymous modules
* Working imports
	* !!!!!
* Fixed small issues with implicit top level module names
* HushCon

## TODO

- JS Monad stuff
- HTTP API

## Rescoping

* How to pitch?
	* Being a product for mathematicians seems too limiting, the market is too small
* Universal runtime
	* App optimization should not be limited by ability to push upstream and wait for users to update software
	* Extensionality of functions has many useful properties
	* Sandboxing for free
	* Optimized interleaving between languages
	* Symbolic execution can be used for application security and static analysis

New slide topics

* Our mission
	* Abstrata’s mission is to fundamentally change how we create and think about code.
* What we do
	* We are creating a platform that assist computer scientists in the process of formalizing theories and proofs.
* A universal runtime
	* Our novel approach to static analysis and formal logic lets us build a runtime with no compromises.
	* Developers can leverage proofs as optimizations to match the performance of assembly while still enforcing isolation.
* The problem today
	* Static analysis tools are common in software development but almost all are unsound.
	* Proof assistants exist but have poor UX and do not scale.
	* There is a lot of duplication of work across linters, vulnerability scanners, virtual machines, compilers, and hardware synthesis.

## Security / Static Analysis

* Semgrep
	* Type system + IR + Transformers
	* Real deep semantic analysis
* CodeQL
	* Just generalized AST / CFG pattern matching in a query language
* Snyk
	* Not open source, looks like basic AI scanning for known vulnerabilities in infra
* SonarQube
	* Enterprise DevOps nonsense
	* Literally has an infinity loop diagram below "Writing clean code"
* Veracode
	* Mostly consulting
* Fortify
	* General application security (consulting?)
	* Marketing pamphlet labelled "White Paper" "Data Sheet" lol
* Angr
	* Really good binary analysis, full symbolic execution, SAT solving
* Mythril
	* Like Angr but for Ethereum
* Checkmarx
	* More closed source enterprise stuff
* S2E
	* Comprehensive application symbolic execution and vulnerability scanning

## Runtimes

* Deno: https://deno.com/
	* Startup
	* Replacement for Node
* Wasmer: https://wasmer.io/
	* Startup
	* Just a WASM runtime and package registry
* Bun: https://bun.sh/
	* Startup
	* Just a frontend for JavaScriptCore
* KLEE: http://klee.github.io/
	* Symbolic execution of LLVM
	* Automatically finds bugs and generates test cases by discovering code paths
* GraalVM / Truffle: https://www.graalvm.org/ / https://www.graalvm.org/22.0/graalvm-as-a-platform/language-implementation-framework/
	* Makes custom interpreters very fast
	* Synchronous interop with Java ecosystem