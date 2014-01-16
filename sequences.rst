Sequences and iteration
=======================

Slices
------

The step argument in slice operators
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

>>> a = [1, 2, 3, 4, 5]
>>> a[::2]  # iterate over the whole list in 2-increments
[1, 3, 5]

The special case x[::-1] is a useful idiom for 'x reversed'.

>>> a[::-1]
[5, 4, 3, 2, 1]


Ellipsis
^^^^^^^^

Python's advanced slicing operation has a barely known syntax element, the
ellipsis.

>>> class C(object):
...  def __getitem__(self, item):
...   return item
...
>>> C()[1:2, ..., 3]
(slice(1, 2, None), Ellipsis, 3)


Slices & Mutability
^^^^^^^^^^^^^^^^^^^

Assigning and deleting slices
"""""""""""""""""""""""""""""

>>> a = range(10)
>>> a
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> a[:5] = [42]
>>> a
[42, 5, 6, 7, 8, 9]
>>> a[:1] = range(5)
>>> a
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> del a[::2]
>>> a
[1, 3, 5, 7, 9]
>>> a[::2] = a[::-2]
>>> a
[9, 3, 5, 7, 1]

Note: when assigning to extended slices (`s[start:stop:step]`), the assigned
iterable must have the same length as the slice.


Copying lists
"""""""""""""

>>> x = [1, 2, 3]
>>> y = x[:]
>>> y.pop()
3
>>> y
[1, 2]
>>> x
[1, 2, 3]

Replacing lists
"""""""""""""""

>>> x = [1, 2, 3]
>>> y = x
>>> y[:] = [4, 5, 6]
>>> x
[4, 5, 6]


Slices as lvalues
^^^^^^^^^^^^^^^^^

This Sieve of Eratosthenes produces a list that has
either the prime number or 0. Elements are 0'd out with the slice assignment in
the loop.

>>> def eras(n):
...     last = n + 1
...     sieve = [0,0] + list(range(2, last))
...     sqn = int(round(n ** 0.5))
...     it = (i for i in xrange(2, sqn + 1) if sieve[i])
...     for i in it:
...         sieve[i*i:last:i] = [0] * (n//i - i + 1)
...     return filter(None, sieve)
>>> eras(5)
[2, 3, 5]

To work, the slice on the left must be assigned a list on the right of the same
length.

Comprehension
-------------

Nested list comprehensions and generator expressions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

>>> [(i,j) for i in range(3) for j in range(i)]
[(1, 0), (2, 0), (2, 1)]
>>> list((i,j) for i in range(3) for j in range(i))
[(1, 0), (2, 0), (2, 1)]


Multiple if statements in comprehension
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The example is form the Udacity course 'Design of Computer Programs' with
instructor Peter Norvig.

>>> import itertools

>>> def imright(h1, h2):
...     "House h1 is immediately right of h2 if h1-h2 == 1."
...     return h1-h2 == 1

>>> def nextto(h1, h2):
...     "Two houses are next to each other if they differ by 1."
...     return abs(h1-h2) == 1

>>> def zebra_puzzle():
...     "Return a tuple (WATER, ZEBRA indicating their house numbers."
...     houses = first, _, middle, _, _ = [1, 2, 3, 4, 5]
...     orderings = list(itertools.permutations(houses)) # 1
...     return next((WATER, ZEBRA)
...                 for (red, green, ivory, yellow, blue) in orderings
...                 if imright(green, ivory)
...                 for (Englishman, Spaniard, Ukranian, Japanese, Norwegian) in orderings
...                 if Englishman is red
...                 if Norwegian is first
...                 if nextto(Norwegian, blue)
...                 for (coffee, tea, milk, oj, WATER) in orderings
...                 if coffee is green
...                 if Ukranian is tea
...                 if milk is middle
...                 for (OldGold, Kools, Chesterfields, LuckyStrike, Parliaments) in orderings
...                 if Kools is yellow
...                 if LuckyStrike is oj
...                 if Japanese is Parliaments
...                 for (dog, snails, fox, horse, ZEBRA) in orderings
...                 if Spaniard is dog
...                 if OldGold is snails
...                 if nextto(Chesterfields, fox)
...                 if nextto(Kools, horse)
...                 )

>>> zebra_puzzle()
(1, 5)


`enumerate`
-----------

Wrap an iterable with enumerate and it will yield the item along with its
index.

>>> a = ['a', 'b', 'c', 'd', 'e']
>>> for index, item in enumerate(a): print index, item
...
0 a
1 b
2 c
3 d
4 e


`enumerate` with different starting index
-----------------------------------------

`enumerate` has partly been covered in this answer, but recently I've found an
even more hidden feature of enumerate that I think deserves its own post
instead of just a comment.

Since Python 2.6, you can specify a starting index to enumerate in its second
argument:

>>> l = ['spam', 'ham', 'eggs']
>>> list(enumerate(l))
[(0, 'spam'), (1, 'ham'), (2, 'eggs')]
>>> list(enumerate(l, 1))
[(1, 'spam'), (2, 'ham'), (3, 'eggs')]


Sequence multiplication and reflected operands
-----------------------------------------------

>>> 'xyz' * 3
'xyzxyzxyz'

>>> [1, 2] * 3
[1, 2, 1, 2, 1, 2]

>>> (1, 2) * 3
(1, 2, 1, 2, 1, 2)

We get the same result with reflected (swapped) operands

>>> 3 * 'xyz'
'xyzxyzxyz'

It works like this:

>>> s = 'xyz'
>>> num = 3

To evaluate an expression s * num interpreter calls `s.__mul__(num)`

>>> s * num
'xyzxyzxyz'

>>> s.__mul__(num)
'xyzxyzxyz'

To evaluate an expression num * s interpreter calls `num.__mul__(s)`

>>> num * s
'xyzxyzxyz'

>>> num.__mul__(s)
NotImplemented

If the call returns NotImplemented then interpreter calls a reflected operation
`s.__rmul__(num)` if operands have different types

>>> s.__rmul__(num)
'xyzxyzxyz'

We can override the `__mul__` method

>>> class MyList(list):
...     def __mul__(self, n):
...         result =  [l * n for l in self]
...         return result if 0 <= n else list(reversed(result))
>>> l = MyList([1, 2, 3])
>>> l * 2
[2, 4, 6]
>>> l * -2
[-6, -4, -2]


Infinite recursion in list
--------------------------

>>> a = [1,2]
>>> a.append(a)
>>> a
[1, 2, [...]]
>>> a[2]
[1, 2, [...]]
>>> a[2][2][2][2][2][2][2][2][2] == a
True


`iter()` can take a callable and a sentinel
--------------------------------------------

The signature of `iter` is `iter(callable, sentinel) -> iterator`.
If a second argument, sentinel, is given to `iter()`, the first argument must be
a callable. The created iterator will call the callable on each call to its
`next()` method, until the callabele return value equal to the sentinel.

>>> import StringIO
>>> fp =  StringIO.StringIO('''1
... 2
... end
... 3
... ''')
>>> for line in iter(lambda: fp.readline().strip(), 'end'):
...     print line.strip()
1
2


Passing generator comprehension to as argument of function
----------------------------------------------------------

>>> def to_list(gen):
...     return list(gen)

>>> to_list(((i,j) for i in range(3) for j in range(i)))
[(1, 0), (2, 0), (2, 1)]

Is the same as:

>>> to_list((i,j) for i in range(3) for j in range(i))
[(1, 0), (2, 0), (2, 1)]
