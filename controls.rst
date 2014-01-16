Control structures
==================

Chaining comparison operators
-----------------------------

>>> x = 5
>>> 1 < x < 10
True
>>> 10 < x < 20
False
>>> x < 10 < x*10 < 100
True
>>> 1 <= x <= 9
True
>>> 4 < x == 5
True


The for...else syntax
---------------------

>>> def find(seq, val):
...     for i in seq:
...         if i == val:
...             print "found"
...             break
...     else:
...         print "not found"
>>> find([1, 2, 3], 2)
found
>>> find([1, 2, 3], 5)
not found

The "else" block will be normally executed at the end of the for loop, unless
the break is called (see http://docs.python.org/ref/for.html).
