---
share: true
---

# Week 19 - 2023

## Done

* Finish adding all the primitives to core
* Ported over some flutter recipes to notes

## TODO

- Practice pitch
- Add Doc/IO monad to core
- Web editor
- Permissions

## Storage

* No more dbPut, only normalizing the compile function applied to a ref
* Ref API should encode all trivial constructor calls (but how?)

Declaration types:

* Axiom (postulate)
* Generalize (I think this is `type x = y z`)
* Field (Record field)
* Primitive (Primitive function def)
* Mutual (Mutual blocks)
* Section (Parameterized sub-modules)
* Apply (Parameterized module application)
* Import
* Open
* FunDef (Actual function definition)
* DataSig (Data signature w/o definition)
* DataDef (Data type definition)
* RecSig
* RecDef
* PatternSynDef (`pattern x = y` stuff, not used by compiler)
* UnquoteDecl (reflection)
* UnquoteDef
* UnquoteData
* ScopedDecl (Arbitrary declaration with a different scope, )

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