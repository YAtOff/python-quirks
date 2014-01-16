============
Pyton quirks
============

# Hello

>>> import __hello__
Hello world...

# Chaining comparison operators:

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


# Be careful with mutable default arguments

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


# The for...else syntax (see http://docs.python.org/ref/for.html )

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
the break is called.


# iter() can take a callable and a sentinel
iter(callable, sentinel) -> iterator
If a second argument, sentinel, is given to iter(), the first argument must be
a callable. The created iterator will call the callable on each call to its
next() method, until the callabele return value equal to the sentinel.

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



# The step argument in slice operators. For example:

>>> a = [1, 2, 3, 4, 5]
>>> a[::2]  # iterate over the whole list in 2-increments
[1, 3, 5]

The special case x[::-1] is a useful idiom for 'x reversed'.

>>> a[::-1]
[5, 4, 3, 2, 1]


# From 2.5 onwards dicts have a special method __missing__ that is invoked for missing items:

>>> class MyDict(dict):
...  def __missing__(self, key):
...   self[key] = rv = []
...   return rv
...
>>> m = MyDict()
>>> m["foo"].append(1)
>>> m["foo"].append(2)
>>> dict(m)
{'foo': [1, 2]}

There is also a dict subclass in collections called defaultdict that does pretty much the same but calls a function without arguments for not existing items:

>>> from collections import defaultdict
>>> m = defaultdict(list)
>>> m["foo"].append(1)
>>> m["foo"].append(2)
>>> dict(m)
{'foo': [1, 2]}


# Readable regular expressions

In Python you can split a regular expression over multiple lines, name your matches and insert comments.

Example verbose syntax (from Dive into Python):

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


# Named formatting

>>> print("The {foo} is {bar}.".format(foo='answer', bar=42))
The answer is 42.


# Passing generator comprehension to as argument of function

>>> def to_list(gen):
...     return list(gen)

>>> to_list(((i,j) for i in range(3) for j in range(i)))
[(1, 0), (2, 0), (2, 1)]

Is the same as:

>>> to_list((i,j) for i in range(3) for j in range(i))
[(1, 0), (2, 0), (2, 1)]


# Nested list comprehensions and generator expressions

>>> [(i,j) for i in range(3) for j in range(i)]
[(1, 0), (2, 0), (2, 1)]
>>> list((i,j) for i in range(3) for j in range(i))
[(1, 0), (2, 0), (2, 1)]


# Multiple if statements in comprehension

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

# Creating new types in a fully dynamic manner

>>> NewType = type('NewType', (object,), {'x': 'hello'})
>>> n = NewType()
>>> n.x
'hello'

which is exactly the same as

>>> class NewType(object):
...     x = 'hello'
>>> n = NewType()
>>> n.x
'hello'


# enumerate

Wrap an iterable with enumerate and it will yield the item along with its
index.

For example:

>>> a = ['a', 'b', 'c', 'd', 'e']
>>> for index, item in enumerate(a): print index, item
...
0 a
1 b
2 c
3 d
4 e

# In-place value swapping

>>> a = 10
>>> b = 5
>>> a, b
(10, 5)

>>> a, b = b, a
>>> a, b
(5, 10)


# Multiplying by a boolean

>>> enabled = True
>>> 'enabled' * enabled
'enabled'
>>> enabled = False
>>> 'enabled' * enabled
''

This is because multiplication coerces the boolean to an integer (0 for False,
1 for True), and in python multiplying a string by an int repeats the string
N times.


# Python's advanced slicing operation has a barely known syntax element, the
ellipsis:

>>> class C(object):
...  def __getitem__(self, item):
...   return item
...
>>> C()[1:2, ..., 3]
(slice(1, 2, None), Ellipsis, 3)


# pow() can also calculate (x ** y) % z efficiently.

There is a lesser known third argument of the built-in pow() function that
allows you to calculate xy modulo z more efficiently than simply doing
(x ** y) % z:

>>> x, y, z = 1234567890, 2345678901, 17
>>> pow(x, y, z)            # almost instantaneous
6

In comparison, (x ** y) % z didn't given a result in one minute on my machine
for the same values.


# You can easily transpose an array with zip.

>>> a = [(1,2), (3,4), (5,6)]
>>> zip(*a)
[(1, 3, 5), (2, 4, 6)]


# enumerate with different starting index

enumerate has partly been covered in this answer, but recently I've found an
even more hidden feature of enumerate that I think deserves its own post
instead of just a comment.

Since Python 2.6, you can specify a starting index to enumerate in its second
argument:

>>> l = ['spam', 'ham', 'eggs']
>>> list(enumerate(l))
[(0, 'spam'), (1, 'ham'), (2, 'eggs')]
>>> list(enumerate(l, 1))
[(1, 'spam'), (2, 'ham'), (3, 'eggs')]


# Partial functions

>>> from functools import partial
>>> bound_func = partial(range, 0, 10)
>>> bound_func()
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> bound_func(2)
[0, 2, 4, 6, 8]


# dict's constructor accepts keyword arguments:

>>> dict(foo=1, bar=2)
{'foo': 1, 'bar': 2}



# Sequence  multiplication and reflected operands

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

To evaluate an expression s * num interpreter calls s.__mul__(num)

>>> s * num
'xyzxyzxyz'

>>> s.__mul__(num)
'xyzxyzxyz'

To evaluate an expression num * s interpreter calls num.__mul__(s)

>>> num * s
'xyzxyzxyz'

>>> num.__mul__(s)
NotImplemented

If the call returns NotImplemented then interpreter calls a reflected operation s.__rmul__(num) if operands have different types

>>> s.__rmul__(num)
'xyzxyzxyz'

We can override the  __mul__ method

>>> class MyList(list):
...     def __mul__(self, n):
...         result =  [l * n for l in self]
...         return result if 0 <= n else list(reversed(result))
>>> l = MyList([1, 2, 3])
>>> l * 2
[2, 4, 6]
>>> l * -2
[-6, -4, -2]

# Implicit concatenation

>>> s = 'Hello ' 'world'
>>> s
'Hello world'

>>> s = ('Hello '
... 'world')
>>> s
'Hello world'


# Assigning and deleting slices:

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

Note: when assigning to extended slices (s[start:stop:step]), the assigned
iterable must have the same length as the slice.


# You can build up a dictionary from a set of length-2 sequences. Extremely
handy when you have a list of values and a list of arrays.

>>> d = dict([ ('foo','bar'),('a',1),('b',2) ])
>>> d == {'a': 1, 'b': 2, 'foo': 'bar'}
True

>>> names = ['Bob', 'Marie', 'Alice']
>>> ages = [23, 27, 36]
>>> d = dict(zip(names, ages))
>>> d == {'Alice': 36, 'Bob': 23, 'Marie': 27}
True


# Creating enums

>>> FOO, BAR, BAZ = range(3)
>>> FOO
0
>>> BAR
1


# "Unpacking" to function parameters

>>> def foo(a, b, c):
...     print a, b, c
>>> bar = (1, 2, 3)
>>> foo(*bar)
1 2 3


# Operators can be called as functions:

>>> from operator import add
>>> print reduce(add, [1, 2, 3, 4, 5, 6])
21




# infinite recursion in list

>>> a = [1,2]
>>> a.append(a)
>>> a
[1, 2, [...]]
>>> a[2]
[1, 2, [...]]
>>> a[2][2][2][2][2][2][2][2][2] == a
True


# Manipulating Recursion Limit

Getting or setting the maximum depth of recursion with sys.getrecursionlimit()
& sys.setrecursionlimit().

We can limit it to prevent a stack overflow caused by infinite recursion.


# Slices & Mutability

Copying lists

>>> x = [1,2,3]
>>> y = x[:]
>>> y.pop()
3
>>> y
[1, 2]
>>> x
[1, 2, 3]

Replacing lists

>>> x = [1,2,3]
>>> y = x
>>> y[:] = [4,5,6]
>>> x
[4, 5, 6]




# Slices as lvalues. This Sieve of Eratosthenes produces a list that has
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


# getattr takes a third parameter

getattr(obj, attribute_name, default)

>>> getattr(int, 'nonexisting', False)
False


# Cast from string using float():

>>> float('NaN')
nan
>>> float('Inf')
inf
>>> -float('Inf')
-inf
>>> float('Inf') == float('Inf')
True
>>> float('Inf') == 1
False
