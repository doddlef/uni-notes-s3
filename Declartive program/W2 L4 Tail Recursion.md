a predicate is **tail recursive** if the only recursive call on any execution of that predicate is the **last code** executed before returning to the caller.

For example, the usual definition of `append/3` is tail recursive, but `rev1/2` is not:
```prolog
append([], C, C).
append([A|B], C, [A|BC]) :-
	append(B, C, BC).

rev1([], []).
rev1([A|BC], CBA) :-
	rev1(BC, CB),
	append(CB, [A], CBA).
```

Prolog performs **tail recursion optimisation (TRO)**, like most declarative languages, which makes recursive predicates behave like loops.

TRO is more often applicable in Prolog than in other languages because of the fact that in Prolog we can use an unbound variable in constructing a term, and only bind it later.

In most languages, after recursively calling `append`, still have to construct a new list cell whose head is the head of the first input, and tail is the result of the recursive call. In Prolog, the list cell `[A|BC]` has already been constructed in the head of the clause, so no computation is needed.

### Stack
Prolog implements call and return using a **stack**. When `a` calls `b`, it creates a fresh **stack frame** for `b`'s local variables and return address. If `b` has nothing left to do after calling `c`, there is no need to preserve `b`'s local variables, Prolog can release `b`'s frame before calling `c`, which is called **`last call optimization`**.

TRO is a special case of last call optimisation where the last call is recursive. Without TRO, this would require a stack frame for each iteration. With TRO, tail recursive predicates execute in constant stack space, just like a loop.

However, **if `b` leaves a choicepoint**, it sits on the stack after `b`'s frame, "freezing" that and all earlier frames. Hence, for TRO to apply, the last call must be deterministic.

The factorial predicate was not tail recursive, as the last thing it does is perform arithmetic.
```prolog
fact(N, F) :-
	(N =:= 0 -> 
		F = 1
	; N > 0,
		N1 is N - 1,
		fact(N1, F1),
		F is F1 * N
	).
```
If-then-else does not leave a choice point. It is created, but removed as soon as the condition succeeds or fails, so `face` would be subject to TRO, if only it were tail recursive.

using current `fact/2`:
- eacb level of recursion stays on the stack, waiting to perform `F is F1 * N`
- have a stack frame from N down to 1
- if N is very large, can easily run out of space
we want to do **all computation before** the recursive call, which can immediately release stack frames.

### Accumulator
we can introducing an **accumulating parameter** or **accumulator**, which is an extra parameter to the predicate that holds a partially computed result. The key is specifying what the accumulator _means_ and how it relates to final result.

For factorial, compute `fact(N1, F1), F is F1 * N` last, so the tail recursive version will need to perform the multiplication too. we define a predicate `fact1(N, A, F)` so that `F` is `A` times the factorial of `N`. Finally, define the original predicate in terms of the new one.
```prolog
fact(N, F) :- fact1(N, 1, F).
fact1(N, A, F) :- 
	( N =:= 0 -> 
		F = A
	; N > 0,
		N1 is N - 1,
		A1 is A * N,
		fact1(N1, A1, F)
	)
```

think about write it as a `while` loop:
```kotlin
var A = 1
while (N > 0) {
	A *= N
	N--
}
if (N = 0) return A
else throw Exception()
```

### Transform
Another approach is to systematically **transform** the non-tail into an equivalent tail recursive predicate. Start by defining a predicate to do the work of the recursive call to `fact/2` and everything following it. 
```prolog {2}
fact1(N, A, F) :-
	fact(N, F2),
	F is F2 * A.
```

Then replace the call to `fact(N, F2)` by the definition of `fact/2`. This is called **unfolding**.
```prolog {2-8}
fact1(N, A, F) :-
	(N =:= 0 -> 
		F2 = 1
	; N > 0,
		N1 is N - 1,
		fact(N1, F1),
		F2 is F1 * N
	),
	F is F2 * A.
```
Move the final goal into both the _then_ and _else_ branches.
```prolog {4, 9}
fact1(N, A, F) :-
	(N =:= 0 -> 
		F2 = 1,
		F is F2 * A
	; N > 0,
		N1 is N - 1,
		fact(N1, F1),
		F2 is F1 * N,
		F is F2 * A
	).
```
Simplify the arithmetic goals.
```prolog {3, 7}
fact1(N, A, F) :-
	(N =:= 0 -> 
		F = A
	; N > 0,
		N1 is N - 1,
		fact(N1, F1),
		F is (F1 * N) * A
	).
```
`F is (F1 * N) * A` can be written as `F is F1 (N * A)`.

the part of computation can be moved before the call to `fact/2`
```prolog {6, 8}
fact1(N, A, F) :-
	(N =:= 0 -> 
		F = A
	; N > 0,
		N1 is N - 1,
		A1 is N * A,
		fact(N1, F1),
		F is F1 * A1
	).
```
Recognise that the last two goals look like the original body of the `fact1/3`, so we replaced those two goals with the clause head with that substitution applied. This is called **folding**.
```prolog {7}
fact1(N, A, F) :-
	(N =:= 0 ->
		F = A
	; N > 0,
		N1 is N - 1,
		A1 is N * A,
		fact1(N1, A1, F)
	).
```

### Accumulating Lists
The tail recursive version of `fact` is a constant factor more efficient, because it behaves like a loop. Sometimes accumulators can make an order difference, if it can replace an operation with a computation of lower asymptotic complexity.
```prolog
rev1([], []).
rev1([A|BC], CBA) :-
	rev1(BC, CB),
	append(CB, [A], CBA).
```
This definition is of quadratic complexity, because for the $n^{th}$ element, we append a list of length $n-1$ to a singleton list, which gives time to $\frac{n(n-1)}{2}$.

The first step is to specify the new predicate. It must combine the work of `rev1/2` with that of `append/3`. The specification is:
```prolog
% rev(BCD, A, DCBA)
% DCBA is BCD reversed, with A appended
% begin the base case, BCD = []
rev([], A, A).

rev([B|CD], A, DCBA) :-
	rev1([B|CD], DCB),
	append(DCB, A, DCBA).

rev([B|CD], A, DCBA) :-
	rev1(CD, DC),
	append(DC, [B], DCB),
	append(DCB, A, DCBA).

rev([B|CD], A, DCBA) :-
	rev1(CD, DC),
	append(DC, [B|A], DCBA).

rev([B|CD], A, DCBA) :-
	rev(CD, [B|A], DCBA).
```

use the `rev/3` predicate to define `rev/2` as:
```prolog
rev(ABC, CBA) :-
	rev(ABC, [], CBA).

rev([], A, A).

rev([B|CD], A, DCBA) :-
	rev(CD, [B|A], DCBA).
```
At each step, `rev/3` removes an element from the head and adds it to the head of the accumulator, whose cost is therefore a constant

in order to take advantage of TRO, we need to ensure that **no choice points** are created. Currently, it faces same problem as `rev1` when the first argument is unbound, for example: `rev(X, [a, b, c])`, infinite probabilities for `[B|CD]`

we can use the same trick
```prolog
rev(ABC, CBA) :-
	same_length(ABC, CBA),
	rev(ABC, [], CBA).
```

### Difference pairs
In Prolog, if you do not know what will come after at the time you call the predicate, you can pass an unbound variable, and bind it when you do know. Thus many predicates intended to produce **a list have two arguments** in addition to the initial input arguments - one denotes the list produced, and the other is what comes after. This is called **difference pair**, because the predicate generates the difference between the first and second of these lists.

For example, the normal to construct a list `[a, b, c]`:
```prolog
append([a], [b], AB),
append(AB, [c], ABC).
```

This method construct immediate list, which costs time. We can keep the "end" as an unbound variable so we can keep adding:
```prolog
?- L-T = [a|T1]-T1,      % Start: L = [a|T1], T = T1
   T1-T2 = [b|T2]-T2,    % Add b
   T2-T3 = [c|T3]-T3,    % Add c
   T3 = [].              % Close the list

L = [a, b, c],
T = [].
```

It just like the `stream` in Java/Kotlin, which av.id repeatedly copying long lists when extending them.

For example, `flatten/3` defines a predicate `flatten(+Tree, -Flattened, ?List)` where `Flattened` is the list consisting of the nodes in `Tree`, with `List` appended to it.
```prolog
flatten(empty, List, List).
flatten(node(L, E, R), Flattened, List) :-
	flatten(L, Flattened, [E | FlattenedR]),
	flatten(R, FlattenedR, List).
```