Functions
=========

Be careful with mutable default arguments
-----------------------------------------

>>> def foo(x=[]):
...     x.append(1)
...     print x
...
>>> foo()
[1]
>>> foo()
[1, 1]
>>> foo()
[1, 1, 1]

Instead, you should use a sentinel value denoting "not given" and replace with
the mutable you'd like as default:

>>> def foo(x=None):
...     if x is None:
...         x = []
...     x.append(1)
...     print x
>>> foo()
[1]
>>> foo()
[1]


Partial functions
-----------------

>>> from functools import partial
>>> bound_func = partial(range, 0, 10)
>>> bound_func()
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> bound_func(2)
[0, 2, 4, 6, 8]


Operators can be called as functions
------------------------------------

>>> from operator import add
>>> print reduce(add, [1, 2, 3, 4, 5, 6])
21


Manipulating Recursion Limit
----------------------------

We can limit it to prevent a stack overflow caused by infinite recursion by
getting or setting the maximum depth of recursion with `sys.getrecursionlimit()`
& `sys.setrecursionlimit()`.


"Unpacking" to function parameters
----------------------------------

>>> def foo(a, b, c):
...     print a, b, c
>>> bar = (1, 2, 3)
>>> foo(*bar)
1 2 3
