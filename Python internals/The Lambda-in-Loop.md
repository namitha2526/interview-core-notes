# Late Binding Closures in Python (The Lambda-in-Loop Trap)

### Overview

Python closures capture variables by reference, not by value.
This causes a subtle but serious bug when using lambda or inner functions inside loops.

Many developers — even experienced ones — get this wrong.

The Bug

Consider this code:
```
funcs = []

for i in range(3):
    funcs.append(lambda: i)

for f in funcs:
    print(f())
```
```
Expected output:
0
1
2

Actual output:
2
2
2
```

This surprises most people.

Why This Happens (The Real Reason)

Python uses late binding for closures.

### That means:

The lambda does not store the value of i when it is created

It stores a reference to the variable i

When the function is finally executed, it looks up i — and by then, the loop has finished and i = 2

So all functions end up pointing to the same final value.

The Correct Fix (Using Default Arguments)

You must bind the value at definition time:
```
funcs = []

for i in range(3):
    funcs.append(lambda i=i: i)

for f in funcs:
    print(f())
```
```
Output:

0
1
2
```

### Why this works:

Default arguments are evaluated at function creation time

Each lambda now gets its own copy of i

Alternative Fix (Cleaner with a helper function)
```
def make_func(x):
    return lambda: x

funcs = [make_func(i) for i in range(3)]

for f in funcs:
    print(f())
```
Why This Matters in Real Code

This bug commonly appears in:

> Callbacks

> Event handlers

> Threads

> Async code

> Button click handlers (GUI apps)

> Closures inside list comprehensions

> Functional programming patterns

It can silently break logic without throwing any error.


If asked about this:

“Python closures use late binding, so variables in lambdas inside loops are evaluated when the function runs, not when it's defined. That’s why we bind values using default arguments like lambda x=i: x.”

