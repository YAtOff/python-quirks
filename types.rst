Data types
==========

Dictionary
----------

`dict`'s constructor accepts keyword arguments
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

>>> dict(foo=1, bar=2)
{'foo': 1, 'bar': 2}


You can build up a dictionary from a set of length-2 sequences
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is extremely handy when you have a list of values and a list of arrays.

>>> d = dict([ ('foo','bar'),('a',1),('b',2) ])
>>> d == {'a': 1, 'b': 2, 'foo': 'bar'}
True

>>> names = ['Bob', 'Marie', 'Alice']
>>> ages = [23, 27, 36]
>>> d = dict(zip(names, ages))
>>> d == {'Alice': 36, 'Bob': 23, 'Marie': 27}
True


Dicts have a special method `__missing__` that is invoked for missing items
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

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

There is also a `dict` subclass in collections called `defaultdict` that does
pretty much the same but calls a function without arguments for not existing
items

>>> from collections import defaultdict
>>> m = defaultdict(list)
>>> m["foo"].append(1)
>>> m["foo"].append(2)
>>> dict(m)
{'foo': [1, 2]}

String
------

Implicit concatenation
^^^^^^^^^^^^^^^^^^^^^^

>>> s = 'Hello ' 'world'
>>> s
'Hello world'

>>> s = ('Hello '
... 'world')
>>> s
'Hello world'


Cast from string using `float()`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

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


Multiplying by a boolean
------------------------

>>> enabled = True
>>> 'enabled' * enabled
'enabled'
>>> enabled = False
>>> 'enabled' * enabled
''

This is because multiplication coerces the boolean to an integer (0 for `False`,
1 for `True`), and in python multiplying a string by an int repeats the string
N times.


Creating enums
--------------

>>> FOO, BAR, BAZ = range(3)
>>> FOO
0
>>> BAR
1


getattr takes a third parameter
-------------------------------

The method signature is `getattr(obj, attribute_name, default)`

>>> getattr(int, 'nonexisting', False)
False


Creating new types in a fully dynamic manner
--------------------------------------------

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
