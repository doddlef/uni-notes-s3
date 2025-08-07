>Recap that:
> $v \models F$ iff $F$ is true under $v$.
> 	$\left\{P \mapsto 1, Q \mapsto 0\right\} \models P \vee Q$
> $A \equiv B$ iff $A$ and $B$ always have equal truth values
> 	$P \rightarrow Q \equiv \neg P \vee q$

$G$ is a **_semantic consequence_** of $F$ **if and only if** every model of $F$ is a model of $G$.
- we write "$F \models G$"
- pronounced "(semantically) entails"
- $F \equiv G$ iff $F \models G$ and $G \models F$

Let $F$ and $G$ be formulas
- $F \models G$ if and only if $\models F \rightarrow G$
- $F \equiv G$ if and only if $\models F \leftrightarrow G$ 

#### Examples
while allow use to conclude $P \rightarrow Q$?

### Tautology and Contradiction
- **`Tautology`**: a logical formula which is always true
- **`Contradiction`**: a formula of propositional logic which is always false

> "if the program works, then the program works."
- it is **true** regardless of what "program" or "works" mean,
- **Valid**: always true.
- **Non-valid**: sometimes false.
- $\models F$ is short for "$F$ is valid".

> "the application is good and application is not good."
- It is **false** regardless of what "the application" or "good" mean,
- **Unsatisfiable**: never true
- **Satisfiable**: sometimes true

> "It is currently raining."
- it is true **if and only if** it is currently raining,
- **`Congingent`**: sometimes true, sometimes false.
- most statements are Contingent

### Substitution
Replacing **all** uses of a propositional variable with a given formula.
- Consider "$P \rightarrow P$".
- Substitute $P$ with "$(Q \wedge R)$"
- Result "$(Q \wedge R) \rightarrow (Q \wedge R)$".
**Substitution preserves validity**
- substitution **preserve unsatisfiability**
	- negation of contradiction is a tautology
- **not** preserve **satisfiability**
	- given $P$, which is satisfiable, but substitute $P$ by $Q \vee \neg Q$ is unsatisfiable

##### substitution preserves logical equivalence
- denote by $F \left[ A := B \right]$ the result of substituting $A$ with $B$ in $F$.
	- $(P \rightarrow P) \left[ P := Q \right]$ is $(Q \rightarrow Q)$.
- Let $F, G, H$ be formulas and $P$ be any propositional variable.
	- if $F \equiv G$, then $F \left[ P := H \right] \equiv G \left[ P := H\right]$
- if $F \equiv G$, then $F$ can be freely replaced with $G$.
	- if $F \equiv G$, then $H \equiv H'$, let $H'$ be the result of replacing an instance of $F$ with $G$ in $H$.
	- result is equivalent: all semantic properties preserved.

### Some Equivalences
- Absorption
	- $P \vee P \equiv P$
	- $P \wedge P \equiv P$
- Commutativity
	- $P \wedge Q \equiv Q \wedge P$
	- $P \vee Q \equiv Q \wedge P$
- Associativity
	- $P \wedge (Q \wedge R) \equiv (P \wedge Q) \wedge R$
	- $P \vee (Q \vee R) \equiv (P \vee Q) \vee R$
- Distributivity
	- $P \wedge (Q \vee R) \equiv (P \wedge Q) \vee (P \wedge R)$
	- $P \vee (Q \wedge R) \equiv (P \vee Q) \wedge (P \vee R)$

$\leftrightarrow$ is also commutative and associative
- Double negation
	- $P \equiv \neg \neg P$
- De Morgan
	- $\neg (P \wedge Q) \equiv \neg P \vee \neg Q$
	- $\neg (P \vee Q) \equiv \neg P \wedge \neg Q$
- Implication
	- $P \rightarrow Q \equiv \neg P \vee Q$
- Contraposition
	- $\neg P \rightarrow \neg Q \equiv Q \rightarrow P$
	- $P \rightarrow \neg Q \equiv Q \rightarrow \neg P$
- Biimplication
	- $P \leftrightarrow Q \equiv (P \wedge Q) \vee (\neg P \wedge \neg Q)$

#### \_Other formula I found
- $P \rightarrow (Q \rightarrow R) \equiv Q \rightarrow (P \rightarrow R)$

$\bot$ is equivalent to any unsatisfiable formula
$\top$ is equivalent to any valid formula