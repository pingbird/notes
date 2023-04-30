---
share: true
---

# Week 17 - 2023

## Done

- Fix notes css, add link to homepage
- Add notes logo
- Add new projects to homepage
- Publish opinion on prime directive
- Fix notes double header
- Finish keyboard opinion

## TODO

- Practice pitch
- Research topics
	- Unison
	- Tess Brainfuck stuff
	- Andrew Kelley
	- plt_amy λ stuff
	- TigerBeetle

## Iota Feedback

* Needs more financial viability
	* Emphasize market
	* Collaboration tool subscriptions
	* Small contracts from aligned research companies (IBM, Anthropic, Ought, Unison)
	* Build something so cool that investors dump money in it regardless
	* Be so cool that investors dump money in it regardless
	* The Mozilla / non-profit approach (eh)
	* Cloud solutions (eh)
	* Consulting gig (eh)
* Basically implementing unison
	* We are building off of an existing language not re-inventing a new one from scratch
	* Iota is more focused on the ergonomics of proofs not general purpose distributed programming
	* Shares a lot of the same ideals (such as storing everything as an AST) but can't take off without giving users a good enough reason to use it over something more pragmatic
		* Rust is a good example of idealistic programming becoming popular, they built a nice systems language with a good ecosystem and unified build system that also happened to be safe
* Need marketing strategy if mathematicians will ever actually use it
	* The bar for tooling in math research is super low so capturing a single area is not hard, but we want to create something DSL-like that can be useful in all areas of math
	* Need feedback from users here
* Propositional equality is very hard
	* Yes

## Market instrument ideas

Lots of existing markets can be very easily formalized into propositions that are compatible with a proof market. Bitcoin mining, for example, can be implemented by creating it's hashing algorithm and bidding on a proof that there exists an input s where hashing s returns something that can mine the block.

Instruments that are essentially proxies to external markets would require a legal framework to ensure transactions are reliably fulfilled, for example with the [RSA Factoring Challenge](https://en.wikipedia.org/wiki/RSA_Factoring_Challenge) we would reach out to companies like RSA Security LLC to have them bid directly on our platform or sign an agreement to award funds.

Bug bounties are an approach that require more work but have higher liquidity and positive social impact. If someone implemented assembly in Iota you could bid on ways to break the software compiled to that assembly.

For example, you could declare propositional data that represents a web browser in an bad state (broken out of sandbox, file accessed, adversary acquired flag, etc.) and make bids on whether there exists an input that can transition the computer with your web browser from a good state into a bad state.

Implementing the semantics of assembly in Agda wouldn't take that much longer as it would in Haskell, OS semantics would be a little harder, but this is a perfectly attainable goal and widely applicable.

You can use both analytical and traditional tools to make money solving proofs (the ask in bid/ask), just providing the input directly in the above example and running the type checker is a sufficient proof.