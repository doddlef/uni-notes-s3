### Automata Theory
### Formal Language Theory

### Basic Symbols
- $\bot$ bottom
- $\top$ top
- $\wedge$ and (conjunction)
- $\vee$ or (disjunction)
- $\rightarrow$ implies (implication)
- $\leftrightarrow$ if and only if 
- $\neg$ not (negation)

#### Conventions
- negative binds stronger than $\wedge$ and $\vee$ 
- $\rightarrow$ is right-associative: $P \rightarrow Q \rightarrow R$ denotes $P \rightarrow (Q \rightarrow R)$
- $P \wedge Q \vee R$ is ambiguous 
![[conventions-tree.png]]

### Boolean Semantics
- **Truth function**: a function from truth values to truth values
	- Boolean truth values: **t** and **f**, also written **1** and **0**, $\top$ and $\bot$
- **truth table**![[truth-table.png]]
Propositional letters are **Boolean variable**
- **Truth assignment**: a function from propositional letters to truth values
	- $v = \{P \mapsto 1, Q \mapsto 0\}$, then we have:
		- $v(P) = 1$
		- $v(Q) = v(R) = ... = v(Z) = 0$
- $v \models \phi$  means $\phi$ is true under $v$

### Logical Equivalence
formulas are _logically equivalent_ iff they have equal truth values under **every** truth assignment
- $F \equiv G$ means $F$ is **logically equivalent** to $G$

a rule is **sound** if every model of the premises is a model of the conclusion