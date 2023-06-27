---
share: true
---

# Week 25 - 2023

## Done

* Call with David
* Ported a ton of the Agda AST to Agda
* Woooo easier serialization with GHC.Generics

## TODO

- 

## David

* Software Enabled Services
* Put formal verification first
* Developer tooling sucks
* Grad students sounds less sexy than talented computer scientists doing QA work
* Prioritize digital assurance
* https://ieeexplore.ieee.org/document/8333100
* https://www.weforum.org/agenda/2023/01/how-to-trust-systems-with-ai-inside/

## Iota

```cpp

struct SExpr {
  uint32_x;
  union {
    uint32_t token;
    uint32_t y;
  }
}

struct SExprRoot {
  std::vector<std::string> tokens;
  std::vector<SExpr> exprs;
}

```

* Business Prop
	* Tech Companies
		* Make code faster and more reliable
	* Finance
		* Quants rely on systems developers to implement high performance trading strategies, we can replace much of that work
	* Data Engineers
		* Our engine can eliminate the overhead of "glue" of piping data throughout an application, see AWS Presto
* Moat
	* Effectiveness of this type of product depends on the data it collects, whoever is first to market wins
	* Proof systems are not very interchangeable, see the success of Wolfram Mathematica
	* Commercial licensing
* New deck