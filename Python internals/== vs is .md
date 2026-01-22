 # " == " vs " is " in Python

is checks object identity while == checks value equality. Since None is a singleton, is None is both safer and semantically correct. == None can fail if __eq__ is overridden.

### Overview:

Python provides two different ways to compare objects:

== → compares values

is → compares identity (memory reference)

Understanding the difference is important for writing correct and efficient code, especially in interviews.

== (Equality Operator)

== checks whether two objects are logically equal by calling the object's __eq__() method.

### Example:

a = [1, 2, 3]
b = [1, 2, 3]

print(a == b)  # True (same values)
print(a is b)  # False (different objects in memory)


So even though the lists look identical, they are stored at different memory locations.

is (Identity Operator)

is checks whether two variables refer to the same object in memory.

### Example:

a = []
b = a

print(a is b)  # True


Here, both a and b point to the same object.

Why is None is the correct way

None is a singleton in Python (only one instance exists).

So the correct way to check is:

if x is None:
    ...


### Not:

if x == None:  # discouraged

Why == None can be dangerous

Because == calls __eq__(), and that method can be overridden:

class Example:
    def __eq__(self, other):
        return True

obj = Example()

print(obj == None)  # True (wrong logic)
print(obj is None)  # False (correct)


This can silently break your code.

### Performance difference:

is → simple pointer comparison → faster

== → function call to __eq__() → slower

In recursive functions (like tree traversal), this small difference can affect performance.

Best Practice (PEP8)

### Always use:

if x is None:

if x is not None:

