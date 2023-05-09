---
share: true
---

# Week 19 - 2023

## Done

* Finish adding all the primitives to core

## TODO

- Practice pitch
- Add Doc/IO monad to core
- 

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