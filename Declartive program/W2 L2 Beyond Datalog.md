### Terms
In Prolog, all data structures are called `terms`, which can be _atomic_ or _compound_, or it can be a variable. Datalog has only atomic terms and variables.

Atomic terms including integers and integers. An atom begins with a **lower** case letter, and can also be written beginning and ending with a single quote, and have any intervening characters.

Compound Term is a _functor_ followed by one or more arguments in parentheses. Functors have the same syntax as atoms. Because Prolog is dynamically typed, each argument of a term can be **any term**, and no need to declare types.
#### Variables
A variable is also a term, denotes a single unknown term. A variable name begins with an **upper** case letter or underscore. 

A single underscore `_` is special: it specifies a different variable each time it appears.

Prolog is a **single-assignment** language: a variable can only be bound once.
#### Ground
A term is a **ground** term if it contains no variables, for example: `3` and `f(a, b)`.
#### Substitutions
a **substitutions** is a mapping from variables to terms. It means consistently replacing all occurrences of each variable in the map with the term it is mapped to.

For example, applying {$X1 \mapsto leaf, X2 \mapsto 1, X3 \mapsto leaf$} to the term `node(X1, X2, X3)` yields the term `node(leaf, 1, leaf)`, which is an _instance_ of the term

Any ground Prolog term has only one instance, while a non-ground terms has an infinite number of instances.
#### Unification
 term results from applying a substitution $\theta$ to a term $t$ is denoted $t\theta$, then a term $u$ is an instance of term $t$ if there is some $\theta$ such that $u = t\theta$

A $\theta$ _unifies_ two terms if $t\theta = u\theta$, for example: substitution {$X \mapsto a, Y \mapsto b$} is a unifier for `f(a, b)` and `f(a, Y)`.
### Arithmetic
Terms like `6 * 7` are just shorthand for `*(6, 7)`, and `=` does not evaluate them, it just unifies them.
```prolog
?- X = 6 * 7.
X = 6*7.

?- X is 6 * 7.
X = 42.
```
Prolog provides usual operators, including:
- `+ - *`
- `/`
- `//`: integer division, rounds toward 0
- `mod`
- `integer float`: coersion (not operators)
- `< =< > >=`
- `=:= =\=`: equal, not equal (only numbers)

### List
empty list is denoted by `[]`.
A non-empty is denoted by `[H|T]`, where `H` is the head, and `T` is the tail.

For example, `[1, 2, 3]` can be denoted by `[1 | [2 | [3 | []]]]` or `[1, 2 | [3 | []]]`.

A proper list_is either empty or not, we can define a predicate to recognise these:
```prolog
proper_list([]).
proper_liet([H|T]) :-
	proper_list(T).
```
 
There is a built in predicate in most systems for appending, but could easily be implemented as:
```prolog
append([], C, C).
append([A|B], C, [A|BC]) :-
	append(B, C, BC)
```
