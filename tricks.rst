Tips and tricks
===============

In-place value swapping
-----------------------

>>> a = 10
>>> b = 5
>>> a, b
(10, 5)

>>> a, b = b, a
>>> a, b
(5, 10)


Multiple assignment to variables
--------------------------------

>>> a = b = c = 10
>>> a
10
>>> b
10
>>> c
10


Readable regular expressions
----------------------------

In Python you can split a regular expression over multiple lines, name your
matches and insert comments.

Example verbose syntax (from `Dive into Python`_):

.. _Dive into Python: http://www.diveintopython3.net/regular-expressions.html 

>>> import re
>>> pattern = '''
... ^                   # beginning of string
... M{0,4}              # thousands - 0 to 4 M's
... (CM|CD|D?C{0,3})    # hundreds - 900 (CM), 400 (CD), 0-300 (0 to 3 C's),
...                     #            or 500-800 (D, followed by 0 to 3 C's)
... (XC|XL|L?X{0,3})    # tens - 90 (XC), 40 (XL), 0-30 (0 to 3 X's),
...                     #        or 50-80 (L, followed by 0 to 3 X's)
... (IX|IV|V?I{0,3})    # ones - 9 (IX), 4 (IV), 0-3 (0 to 3 I's),
...                     #        or 5-8 (V, followed by 0 to 3 I's)
... $                   # end of string
... '''
>>> if re.search(pattern, 'M', re.VERBOSE):
...     print "found"
found


Named formatting
----------------

>>> print("The {foo} is {bar}.".format(foo='answer', bar=42))
The answer is 42.


`pow()` can also calculate (x ** y) % z efficiently
---------------------------------------------------

There is a lesser known third argument of the built-in `pow()` function that
allows you to calculate xy modulo z more efficiently than simply doing
(x ** y) % z:

>>> x, y, z = 1234567890, 2345678901, 17
>>> pow(x, y, z)            # almost instantaneous
6

In comparison, (x ** y) % z didn't given a result in one minute on my machine
for the same values.


You can easily transpose an array with `zip()`
----------------------------------------------

>>> a = [(1,2), (3,4), (5,6)]
>>> zip(*a)
[(1, 3, 5), (2, 4, 6)]
