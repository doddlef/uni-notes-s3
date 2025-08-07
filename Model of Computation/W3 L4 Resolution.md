if we have $P \rightarrow F$ and $\neg P \rightarrow G$, we can conclude that $F \vee G$. The condition can be write as $\neg P \vee F$, and $P \vee G$, for example
> if it is raining, I will bring an umbrella
> if it is not raining, I will play football
> so we can conclude, I will either bring an umbrella or play football

This is called **Resolution**.

For example, we have three statements $B \vee \neg A$, $B \vee A$, and $\neg B$.
we can do resolution:
$\because (B \vee A) + \neg B$, $\therefore A$
	this two statements can only be true, if A is true
$\because (B \vee \neg A) + A$, $\therefore B$
$\because B + \neg B$, $\therefore \bot$

using the calculation method 
$(B \vee A) \wedge \neg B = (B \wedge \neg B) \vee (A \wedge \neg B) = A \wedge \neg B$
$(A \wedge \neg B) \wedge (B \vee \neg A) = (A \wedge \neg B \wedge B) \vee (A \wedge \neg B \wedge \neg A) = \bot \vee \bot = \bot$

A **refutation** of $... \wedge ... \wedge ...$ showing that it is not true.
- to show $F$ is valid, refute $\neg F$: $\models F$ iff $\neg F \models \bot$
- to prove $F \models G$, refute $F \wedge \neg G$, or $\neg (F \rightarrow G)$: $F \models G$ iff $F \wedge \neg G \models \bot$

another example:![[./assets/refutate.png]]
**Do not cancel multiple variables at once**
- $(P \vee Q) \wedge (\neg P \vee \neg Q)$ is satisfiable, but cancel bother will get a $\bot$

### Propositional resolution
a resolution proof of $C_m$ from wffs $P_1, ..., P_n$ is a **string** of the form
	$P_1, ..., P_n \vdash C_1, ..., C_m$
where each $C_i$ is either a **copy** of some $P_j$, or otherwise follows by **resolution** from any two wffs **earlier in the string**

for example:
- $P \vdash P$
- $P, \neg P \vdash \bot$
- $(P \vee Q), \neg P \vdash Q$

we write $\Sigma \vdash_R F$ to mean "there is a resolution proof of $F$ from the set of premises $\Sigma$"
	if $\Sigma \vdash_R F$, then $\Sigma \models F$
> There is a resolution proof of F using only the formulas in set $\Sigma$
> Using only resolution and those premises in the set, I can derive F
> 	**If** you can derive $F$ by resolution,
> 	**then** $F$ must be logically true whenever all of $\Sigma$ is true.
### Conjunctive Normal Form (CNF)
**Literal**: a propositional atom or its negation
**(Disjunctive) clause**: disjunction ($\vee$) of literals
**CNF**: conjunction ($\wedge$) of disjunctive clauses
> $(A \vee \neg B) \wedge (B \vee C \vee D) \wedge A$

**Every propositional formula has an equivalent CNF**
#### Negation Normal Form (NNF) to CNF
**NNF**: only connectives are $\neg$, $\wedge$ and $vee$. $\neg$ only in front of variables
> $(\neg A \vee (B \wedge \neg C)) \vee (C \wedge (B \vee D))$

to get NNF:
1. eliminate $\leftrightarrow$
2. eliminate $\rightarrow$
3. push $\neg$ inward
4. eliminate $\neg \neg$
to get CNF from NNF, distribute $\vee$ over $\wedge$

for example:
$(\neg P \wedge (\neg Q \rightarrow R)) \leftrightarrow S$
$\equiv ((\neg P \wedge (\neg Q \rightarrow R)) \rightarrow S) \wedge (S \rightarrow (\neg P \wedge (\neg Q \rightarrow R)))$
$\equiv ... \equiv$
$\equiv (P \vee \neg Q \vee S) \wedge (P \vee \neg R \vee S) \wedge (\neg S \vee \neg P) \wedge (\neg S \vee Q \vee R)$

### Satisfiability Algorithm
Resolution is **Refutation-Complete**:
every unsatisfiable set of clauses has a resolution refutation
	let $\Sigma$ be a set of clauses, if $\Sigma \models \bot$, then $\Sigma \vdash_R \bot$.

this give us an algorithm:
1. convert formula into suitable form
2. repeatedly apply resolution
	- derive $\bot$: **unsatisfiable**
	- cannot derive anything new: **satisfiable**

common redundancies:
- Duplicate variables: $P \vee P$
- Tautologies: $P \vee \neg P \vee Q$
- Subsumptions: $(P \vee \neg Q \vee R) \wedge (P \vee R)$

### Empty Disjunction
we can represent CNF as set of sets of literals, which is **Clausal form**
> CNF: 
> 	$(P \vee \neg Q \vee S) \wedge (P \vee \neg R)$
> Clausal form:
> 	$\left\{ \left\{ P, S, \neg Q \right\}, \left\{ P, \neg R \right\} \right\}$

Let $A$ and $B$ be propositional variables
- $\left\{ A, B \right\}$ represents the clauses $A \vee B$
- $\left\{ A \right\}$ represents clauses $A$

What **disjunctive clauses** does {} represent? (sometimes written $\emptyset$)
- **Natural choice**: $\bot$
Disjunction is true iff **at least one** disjunct is true

Let $C_1$ and $C_2$ be clauses
- $\left\{ C_1, C_2 \right\}$ represents the CNF formula $C_1 \wedge C_2$
- $\left\{C_1\right\}$ represents the
What **CNF** does {} represent?
- **Natural choice**: $\top$

- Empty conjunction ($\wedge$) is **valid**
- Empty disjunction ($\vee$) is **unsatisfiable**
- The empty set of clauses if **valid**
- A set of clauses containing the empty clause is **unsatisfiable**

Let $P$ be a propositional variable, $C_1, C_2$ be clauses without $P$
- $C_1 \cup \left\{ P \right\}$, $C_2 \cup \left\{ \neg P \right\}$
- resolve $C_1 \cap C_2$