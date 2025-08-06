**Logic** are based on the concept of a _relation_, captures a relationship among a number of individuals, are the _predicate_ that relates them

### Relations
> `parent(queen_elizabeth, prince_charles)`

specifies parenthood relation, which relates parents to children. The name of a relation is called a **`predicate`**, predicates/relations can have any positive arity, for example, `person(queen_elizabeth)`.

### Facts
a statement such as: `parent(queen_elizabeth, prince_charles)` is called **`fact`**, and it may take many facts to define a relation, like to define the royal family, we need to define many parent relations.

### Queries
once code is loaded, use or test it by issuing **`queries`** at the prompt, which looks like a fact. When written in a source file and loaded into Prolog, it is treated as a true statement, at the prompt, it is treated as a query, asking if it is true

```prolog title="family.pl"
parent(a, b).
parent(b, c).
parent(a, b2).
parent(b2, c2).
```

```queries title="queries"
?- [family]
%% loaded the source file into Prology at the Prolog prompt (?-) %%
% family compiled 0.00 sec, 4 clauses
true
?- parent(a, b)
true 
%% execute a query, which is true %%
?- parent(a, c)
false
```

### Variables
each predicate argument may be a **`variable`**, which in Prolog begins with a capital letter or underscore. A query containing variable asks if there exists a value for that variable that makes the query true, and prints these values. 
If there is more than one answer, prints them one at a time. Typing semicolon for more solutions
```queries
?- parent(a, X).
X = a ;
X = b.
?- parent(X, Y).
X = a,
Y = b ;
...
```

queries, or goals, can be combined into more complex goals, by combining multiple goals to separate them with a comma, which asks Prolog for all bindings for the variables that satisfy all of the goals
```queries
?- parent(a, X), parent(X, b).
X= a.
```

### Equality
written `=` and used as an infix operator, can be used both to bind variables and to check for equality: Prolog is **single-assignment**, once bound, variable cannot be reassigned
```prolog
?- X = 7.
X = 7.

?- a = b.
false.

?- X = 7, X = a.
false

?- X = 7,  Y = 8, X = 7.
false
```

### Negation
written `\+` and used a a prefix operator, has higher precedence than conjunction and disjunction. Be sure to leave a space between the `\+` and an open parenthesis.
```prolog
?- parent(X, a), \+ X = b.
%% who is the parents of a other than b %%
```

**Dis-equality** is written as `\=`, so `X \= Y` is the same as `\+ X = Y`.

### Closed World Assumption
Prolog assume all true things can be derived from program.

### Negation as failure
executes `\+ G` by first trying to prove `G`, and succeeds if this fails

### Order
ensure all variables in a negated goal are bound before the goal is executed.

### Junction
Disjunction is written `;`, and Conjunction is `,` which has higher precedence than disjunction

### Rules
relations can be defined using **`rules`** as well as facts, has the form `Head :- Body`, where `Head` and `Body` are goals, with `Body` possibly compound. The `:-` is read "if", and the clause means that the `Head` is true if the `Body` is.
For example: `grandparent(X, Z) :- parent(X, Y), parent(Y, Z).`

### Recursion
Rules can be recursive: _A person's ancestors are their parents and the ancestors of their parents_
```prolog
ancestor(Anc, Desc) :- 
	parent(Anc, Desc).
ancestor(Anc, Desc) :-
	parent(Parent, Desc),
	ancestor(Anc, Parent).
```

When call `ancestor(a, b).`, it first check if the first one is `ture`, if it success, it return `true`. If it fails, it will more to next query, which is `parent(Parent, Desc), ancestor(Anc, Parent).`, in which, it first find possible variables for `Parent`, supposed it finds `c`, then it try `ancestor(a, c)`, recursive until fail or success.
### Datalog
the fragment of Prolog discussed so far, which omits data structures. It is a generalisation of what is provided by relational databases.