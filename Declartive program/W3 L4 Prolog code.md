#### List Reverse
```prolog
rev1([], []).
rev1([A|BC], CBA) :-
	rev1(BC, CB),
	append(CB, [A], CBA).
```

suppose we given `[a, b, c]`.
1. `A` is assigned as `a`, `BC` is the rest of list `[b, c]`.
2. `BC` will be reversed again, which given `[c, b]` and assigned to `CB`.
3. then append the `CB` to `[A]`, and assigned it to `CBA`, which is the "return" value 

The **mode** of a Prolog goal says which argument are bound (inputs) and which are unbound (outputs) when predicate is called
`rev1/2` works as intended when the first argument is ground and the second i free, but not for the opposite mode

### Debugger
To understand the debugger, first need to understand the **`Byrd box model`**.
A conventional language has only one way to enter and one way to exit, Prolog has two of each.
The four debugger ports are:
- initial entry: `call`
- successful completion: `exit`
- backtrack into goal: `redo`
- final failure: `fail`

turn on debugger with `trace`, and off with `nodebug`.
```prolog
?- trace, rev1([a, b, c], X).
   Call: (13) rev1([a, b, c], _16492) ? creep
   Call: (14) rev1([b, c], _18298) ? creep
   Call: (15) rev1([c], _19186) ? creep
   Call: (16) rev1([], _20074) ? creep
   Exit: (16) rev1([], []) ? creep
   Call: (16) lists:append([], [c], _19186) ? creep
   Exit: (16) lists:append([], [c], [c]) ? creep
   Exit: (15) rev1([c], [c]) ? creep
   Call: (15) lists:append([c], [b], _18298) ? creep
   Exit: (15) lists:append([c], [b], [c, b]) ? creep
   Exit: (14) rev1([b, c], [c, b]) ? creep
   Call: (14) lists:append([c, b], [a], _16492) ? creep
   Exit: (14) lists:append([c, b], [a], [c, b, a]) ? creep
   Exit: (13) rev1([a, b, c], [c, b, a]) ? creep
X = [c, b, a].
```

we could try "backwards" mode
```prolog
?- trace, rev1(X, [a]).
   Call: (13) rev1(_11546, [a]) ? creep
   Call: (14) rev1(_13324, _13326) ? creep
   Exit: (14) rev1([], []) ? creep
   Call: (14) lists:append([], [_13322], [a]) ? creep
   Exit: (14) lists:append([], [a], [a]) ? creep
   Exit: (13) rev1([a], [a]) ? creep
X = [a] ;
   Redo: (14) rev1(_13324, _13326) ? creep
   Call: (15) rev1(_21168, _21170) ? creep
   Exit: (15) rev1([], []) ? creep
   Call: (15) lists:append([], [_21166], _13326) ? creep
   Exit: (15) lists:append([], [_21166], [_21166]) ? creep
   Exit: (14) rev1([_21166], [_21166]) ? creep
   Call: (14) lists:append([_21166], [_13322], [a]) ? creep
   Fail: (14) lists:append([_21166], [_13322], [a]) ? creep
   Redo: (15) rev1(_21168, _21170) ? creep
   Call: (16) rev1(_28346, _28348) ? 
   % repeat...
```
the problem is that the goal `rev1(X, [a])`, resolves to the goal `rev1(BC, CB), append(CB, [A], [a]).` call `rev1(BC, CB)` produces an infinite backtracking sequence of solutions, but it does not know about the value of list or length, like {BC $\mapsto$ \[], CB $\mapsto$ \[]}, {BC $\mapsto$ \[Z], CB $\mapsto$ \[Z]}, ..., for each of these solutions, we call `append(CB, [A], [a]).`

`append([], [A], [a])` succeeds, However, `apend([Z], [A], [a])` fails, which is an infinite backtracking loop.

in forwards, `BC` is assigned as `[a]`, which has limited probabilities.

we can fix this by executing the body goals in other order:
```prolog
rev2([], []).
rev2([A|BC], CBA) :-
	append(CB, [A], CBA),
	rev1(BC, CB).
```
1. It sees `rev2([A|BC], [a])`
2. Then it tries to **unify** `append(CB, [A], [a])`
	- this **constraints** `CB` and `A` early
	- the only solution is `CB = []`, `A = a`

But this will not work in forward direction.
1. it match `A = a`, `BC = []`
2. then try `append(CB, [a], CBA).`
	- what is `CB`?
	- this produce infinity probabilities, try from `[]`, ...

The solution is to ensure that when `rev1` is called, **the first argument is always bound to a list**. We do this by observing that **the length of a list must always be the same as the reverse**, bound to lists of the same fixed length.
```prolog
rev3(ABC, CBA) :-
	same_length(ABC, CBA),
	rev1(ABC, CBA).

same_length([], []).
same_length([_|Xs], [_|Ys]) :-
	same_length(Xs, Ys).
```

### Managing non-determinism
a common mistake in defining factorial
```prolog
fact(0, 1).
fact(N, F) :-
	N1 is N - 1,
	fact(N1, F1),
	F is N * F1.
```
`fact(5, F)` has only one solution, but Prolog will still looking for another
```prolog
?- fact(5, F).
F = 120 ;
ERROR: Out of local stack
```
even if one clause applies, later clauses are still tried. After finding `0! = 1`, Prolog will try compute `-1!`, `-2!`, ... We need to ensure each clause is a correct definition, as current definition `n! = n * (n-1)!` is wrong for `n < 1`.
```prolog
fact(0, 1).
fact(N, F) :-
	N > 0,
	N1 is N - 1,
	fact(N1, F1),
	F is N * F1.
```

this is correct, but it could be more efficient.

when a clause succeeds but there are later clauses that could possibly succeed, Prolog will leave a **`choicepoint`** so it can later backtrack and try later.

in this case, backtracking to second clause will fail unless `N > 0`, which is quick. However, as long as choicepoint exists, it inhibits the very important last call optimisation. Therefore, it is important to may your recursive predicates not leave choicepoints when they should be deterministic.

In this case, `N = 0` and `N > 0` are **mutually exclusive**, so at most one clause can apply, so it should not leave a choicepoint.

### If-then-else
we can avoid the choice point using if-then-else:
```prolog
(Condition -> ThenClause ; ElseClause)

fact(N, F) :-
	(N =:= 0 -> 
		F = 1
	; N > 0,
		N1 is N - 1,
		fact(N1, F1),
		F is F1 * N
	).

% =:= is the '==' in Prolog
```
`->` is treaded as a conjunction (`,`), except that when it is crossed, any alternative solutions of the goal before the `->`, as well as any alternatives following the `;` are forgotten. Conversely, if the goal before `->` fails, the goal after it is tried. However, you should prefer indexing and avoid if-then-else, when you have a choice.

