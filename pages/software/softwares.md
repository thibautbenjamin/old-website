---
layout: page
title: Software
permalink: /software/
---
- #### Frama-C
Since 2021, I develop on the open-source Frama-C software which gives a framework for combining different formal methods for verifying C programs. I mostly work in the E-ACSL plugin specialising in runtime assertion checking. For more information, visit the [frama-c homepage](https://frama-c.com/) or the [E-ACSL page](https://frama-c.com/fc-plugins/e-acsl.html). Also check the public repository on [Gitlab](https://git.frama-c.com/pub/frama-c).

- #### CaTT- An infinity categorical coherence typechecker
CaTT is a type theory whose models are weak omega-categories, I have implemented
it in OCaml. The development page for the project is available on
[GitHub](https://github.com/thibautbenjamin/catt) and there is an [online
version](https://thibautbenjamin.github.io/catt/) available for quick tests

- #### CaTT-Formalization
An agda formalization of the type theory CaTT, as a special case of a framework that I call free globular type theories. This provides a certified interpreter for the bare language CaTT (without syntactic sugar), as well as a setup to prove properties such as decidability of type checking, uniqueness of derivations, and all the lemma on which the categorical analysis of the theory relies. This project is available on [GitHub](https://github.com/thibautbenjamin/catt-formalization)

- #### Formalization of representation of symmetric groups
During a research internship under the supervision of [Florent
Hivert](https://www.lri.fr/~hivert/), I have formalized the computation of the
representations of the symmetric groups and their characters using Young
tableaux, in Coq with ssreflect (now mathcomp). The project is available on
[GitHub](https://github.com/thibautbenjamin/ReprSymGroup), note however that it is not
maintained anymore and is outdated, it has been intergrated to
[Florent’s development](https://github.com/hivert/Coq-Combi) of algebraic combinatorics.
