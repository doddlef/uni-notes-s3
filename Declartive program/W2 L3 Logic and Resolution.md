in the mind of a logic program:
- each constant stands for an entity in the "domain of discourse"
- each functor stands for a function from _n_ entities to one entity in the domain of discourse
- each predicate of arity _n_ stands for a particular relationship between _n_ entities in the domain of discourse

This mapping from the symbols in the program to the "world" of the program is called an _interpretation_.

The meaning of the clause: `grandparent(A, C) :- parent(A, B), parent(B, C).` is: for all the terms that `A` and `C` may stand for, `A` is a grandparent of `C` if there is a term `B` such that ... 

In mathematical notation: $\forall A \forall C: grandparent(A, C) \leftarrow \exists B: parent(A, B) \wedge parent(B, C)$.

A predicate is defined by a finite number of clauses, each of which is in the form of an implication.

### Semantics of logic programs
A logic program `P` consists of a set of clauses (facts and rules). The semantics of this program is the set of its _logical consequences_ as ground clauses. A ground clause `a` is a consequence of `P` if `P` makes `a` true.

You can find the semantics of a logic program `P` by successively applying the _immediate consequence operartor $T_P$_, which takes a set of ground unit clauses $C$ and produces the set of ground unit clauses implied by $C$ as an immediate consequences.

For each clause $H \leftarrow G_1, ..., G_a$, if $C$ contains of $G_1, ..., G_n$, then the corresponding instance of $H$ is also in the result. For example: if $P = \{q(X, Z) \leftarrow p(X, Y), p (Y, Z)\}$ and $C = \{p(a, b), p(b,c), p(c,d)\}$, then $T_P(C) = C \cup \{q(a,c), q(b,c)\}$.

### Unification
Recall that _unifier_ for two terms is a substitution that, when applied to both terms, makes them identical.

Two terms can be unified if:
- one is an unbound variable, in which case unification binds it to the other term;
- both terms are identical
- the terms are compound, with the same functor, and their arguments can be unified pairwise.
Otherwise, unification fails

### Resolution
Input a goal $G$ and a program $P$, to output an instance of $G$ hat is a logical consequence of $P$, or _false_:
	initialize the **resolvent** to $G$.
	_while_ resolvent is non-empty _do_:
		choose a goal $A$ from the resolvent
		choose a clause $A' \leftarrow B_1, ..., B_n$ from $P$ s.t. $A$ unifies with $A'$ via $\theta$. (exit loop if none exists)
		replace $A$ by $B_1, ..., B_n$ in resolvent
		apply $\theta$ to resolvent and to $G$.
	_if_ resolvent is empty, _then_ output $G$; _else_ output _false._

For example: to determine if Elizabeth is Harry's grandparent:
```prolog
grandparent(e, h).
```
we unify goal with clause head `grandparent(X, Z)`, apply the substitution to the clause, yielding the _resolvent_. Since the goal is identical to the resolvent head, we can replace it with the body:
```prolog
parent(e, Y), parent(Y, h).
```

Now we have two goal to fills, and pick the second first, which has only two clause can successfully resolve it
```prolog
parent(c, h).
parent(d, h).
```

Choose the second one again, so applying a $\theta: \{Y \mapsto d\}$, where no clause unifies with this query, so resolution fails. We must _backtrack_ and try the first clause, which matching the clause, and leaving nothing more to prove, which succeeds.

This can be shown as a search tree:
![[./assets/search-tree.png]]

#### Order of execution
The order in which goals are resolved, and clauses are tried does not matter for correctness, but matter for efficiency. In this example, resolving `parent(e, Y)` before `parent(Y, h)` is more efficient, because there is only one clause matching the former.

#### SLD resolution
At each resolution step we must make two decisions:
1. which goal to resolve
2. which clauses matching the selected goal to pursue
Our procedure was somewhat haphazard when decisions needed to be made. For pure logic programming, this does not matter for correctness. All goals will need to be resolved eventually. All matching clauses may need to be tried; the order determines the order solutions are found.

Prolog always selects the first goal to resolve, and always selects the first matching clause to pursue first.

#### Backtracking
When there are multiple clauses matching a goal, Prolog must remember which one to go back to if necessary. It must be able to return the state it was when the first clause was selected. This is all done with a **choice point**.

When a goal fails, Prolog **backtrack** to the most recent choicepoint, removing all variables bindings made singe the point, return them to the unbound state.

### Indexing
a mechanism that helps efficiently select relevant rules or facts when answering a query, reducing the number of clauses that need to be checked.
- Prolog searches facts and rules sequentially by default (linear)
- Indexing optimizes this by creating a lookup table
- Most implementations index based on **predicate name/arity**, plus ground terms in the **first argument** of a predicate.
- SWI-Prolog currently use **Just-In-Time(JIT) Indexing**, which dynamically adjusts based on call patterns.
it changes performance but not semantics.

For example:
```prolog
color(apple, red).
color(banna, yellow).
shape(banana, curved).
...

% when executing the query
?- color(X, yellow).
```
It will only attempt to unify with `color/2` clauses in the program.

To optimization: place the most selective argument first for better indexing.
