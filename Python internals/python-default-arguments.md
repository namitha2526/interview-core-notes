Mutable Default Arguments & Why None is the Safe Pattern

### Overview:

A common Python bug happens when using mutable objects (like lists, dicts, sets) as default function arguments.

This can silently introduce shared state across function calls and cause unexpected behavior.

Most people know the fix.
Very few understand the why.

The Bug
```
def add_item(item, items=[]):
    items.append(item)
    return items
```

Looks fine. But watch this:
```
print(add_item(1))  # [1]
print(add_item(2))  # [1, 2]   ❌ unexpected
print(add_item(3))  # [1, 2, 3] ❌ keeps growing
```

Each call should have started fresh.
Instead, data is leaking between calls.

Why This Happens (The Real Reason)

Default arguments in Python are evaluated once — at function definition time, not at runtime.

So this:
```
def add_item(item, items=[]):
```
`
Is effectively:

One list object created in memory

The same list reused every time the function is called

Every call mutates the same shared object

This is not a bug in Python.
It’s a design decision for performance.

The Correct Pattern (Using None)
```
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

Now:
```
print(add_item(1))  # [1]
print(add_item(2))  # [2]
print(add_item(3))  # [3]
```

Each call gets a fresh list.
No shared state. No hidden bugs.

Why This Matters in Real Code

This issue commonly breaks:

APIs with optional list/dict parameters

Data pipelines

Caching logic

ML preprocessing functions

Backend services

Utility functions reused across modules

These bugs are hard to trace because:

The function appears correct

No exception is thrown

Behavior degrades over time

This is the kind of bug that shows up in production, not in toy examples.

If asked:

Why use None instead of [] as a default argument?

Say:

“Default arguments are evaluated once at function definition time. Mutable defaults like lists persist across calls and cause shared state bugs. Using None avoids this by creating a new object at runtime.”

### Rule of Thumb

Never do this:

def func(x=[]):        # ❌ dangerous
def func(x={}):        # ❌ dangerous
def func(x=set()):     # ❌ dangerous


Always do this:

def func(x=None):      # ✅ safe
    if x is None:
        x = []
