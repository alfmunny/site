---
title: Fluent Python - Chapter 2. An Array of Sequences
date: 2020-09-10 23:30
categories: 
- [Book Notes, Fluent Python]
tags:
- Python
---

Container sequences: can hold different types. They contain references to the objects.

> list, tuple, collections.deque

Flat sequences: can hold one type. They physically sotre the value.

> str, bytes, bytearray, memoryview, array.array

Another way of grouping:

Mutable seuqences:

> list, byte, array.array, collections.deque and memoryview

Immutable sequences:

> tuple, str, bytes

<!--more-->

## List Comprehensions and Generator Expressions

**Readability**

List comprehension is meant to do one thing only: to build a new list. It is possible to abuse it to write very incomprehensible code, you should avoid it.

```python
symbols = '$¢£¥€¤'
codes = [ord(symbol) for symbol in symbols]
codes
```

```python
[36, 162, 163, 165, 8364, 164]
```

No leak in Python 3

```python
x = 'ABC'
dummy = [ord(x) for x in x]
x
```

```python
ABC
```

```python
dummy
```

```python
[65, 66, 67]
```

**Versus map and filter**

```python
beyond_ascii = [ord(s) for s in symbols if ord(s) > 127]
beyond_ascii
```

```python
[162, 163, 165, 8364, 164]
```

```python
beyond_ascii = list(filter(lambda c: c > 127, map(ord, symbols)))
beyond_ascii
```

```python
[162, 163, 165, 8364, 164]
```

**Cartesian Products**

Multiple Loops

```python
colors = ['black', 'white']
sizes = ['S', 'M', 'L']
tshirts = [(color, size) for color in colors for size in sizes]
tshirts
```

```python
[('black', 'S'), ('black', 'M'), ('black', 'L'), ('white', 'S'), ('white', 'M'), ('white', 'L')]
```

We can also arrange them by size

```python
colors = ['black', 'white']
sizes = ['S', 'M', 'L']
tshirts = [(color, size) for size in sizes for color in colors]
tshirts
```

```python
[('black', 'S'), ('white', 'S'), ('black', 'M'), ('white', 'M'), ('black', 'L'), ('white', 'L')]
```

**Generator Expressions** To initialize tuples, arrays, and other tyoes of sequences, you can use Generator Expressions (genexps).

Genexps saves memory because it yields items one by one.

Generator Expressions use the same syntax as list comprehension, but are enclosed in parentheses rather than brackets.

```python
symobls = '$¢£¥€¤'
tuple(ord(symbol) for symbol in symbols)
```

```python
(36, 162, 163, 165, 8364, 164)
```

If you have millions of items, only to feed the loop, genexp can save the expense of building a list for that.

```python
colors = ['black', 'white']
sizes = ['S', 'M', 'L']
for tshirt in ('%s %s' % (c, s) for c in colors for s in sizes):
    print(tshirt)
```

```python
black S
black M
black L
white S
white M
white L
```

## Tuples Ate Not Just Immutable Lists

Tuples do double duty:

1.  immutable lists
2.  as records with no field names

### Tuple as Records

```python
lax_coordinates = (33.9425, -118.408056)
city, year, pop, chg, area = ('Tokyo', 2003, 32450, 0.66, 8014)
traveler_ids = [('USA', '31195855'), ('BRA', 'CE342567'), ('ESP', 'XDA205856')]
for passport in sorted(traveler_ids):
    print("%s/%s" % passport)
```

```python
BRA/CE342567
ESP/XDA205856
USA/31195855
```

```python
for country, _ in traveler_ids:
    print(country)
```

```python
USA
BRA
ESP
```

### Tuple Unpacking

**parallel assignment**

```python
lax_coordinates = (33.9425, -118.408056)
latitude, longitude = lax_coordinates
latitude
```

```python
33.9425
```

```python
longitude
```

```python
-118.408056
```

**swap**

```python
a = 1
b = 2
b, a = a, b
print(a, b)
```

```python
2 1
```

**Prefixing an argument with a start**

```python
divmod(20, 8)
```

```python
(2, 4)
```

```python
t = (20, 8)
divmod(*t)
```

```python
(2, 4)
```

```python
quotient, remainder = divmod(*t)
quotient, remainder
```

```python
(2, 4)
```

**Focus on certain part**

```python

```

```python
import os
_, filename = os.path.split('/home/luciano/.ssh/idrsa.pub')
filename
```

```python
idrsa.pub
```

**Using \* to grab excess items**

Without a \*, it ignore the rest elements while unpacking.

```python
a, b, rest = range(5)
a, b, rest
```

```python
(0, 1, [2])
```

```python
a, b, *rest = range(5)
a, b, rest
```

```python
(0, 1, [2, 3, 4])
```

```python
a,  b, *rest = range(3)
a, b, rest
```

```python
(0, 1, [2])
```

Any place

```python
a, *body, c, d = range(5)
a, body, c, d
```

```python
(0, [1, 2], 3, 4)
```

```python
*head, b, c, d = range(5)
head, b, c, d
```

```python
([0, 1], 2, 3, 4)
```

### Nested Tuple Unpacking

Unpacking nested tuple, like (a, b, (c, d))

```python
metro_areas = [
    ('Tokyo', 'JP', 36.933, (35.689722, 139.691667)),
    ('Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)),
    ('Mexico City', 'MX', 20.142, (19.433333, -99.133333)),
    ('New York-Newark', 'US', 20.104, (40.808611, -74.020386)),
    ('Sao Paulo', 'BR', 19.649, (-23.547778, -46.635833)),
]

print('{:15} | {:^9} | {:^9}'.format('', 'lat.', 'long.'))
fmt = '{:15} | {:9.4f} | {:9.4f}'

for name, cc, pop, (latitude, longitude) in metro_areas:
    if longitude <= 0:
        print(fmt.format(name, latitude, longitude))
```

                    |   lat.    |   long.  
    Mexico City     |   19.4333 |  -99.1333
    New York-Newark |   40.8086 |  -74.0204
    Sao Paulo       |  -23.5478 |  -46.6358

### Named Tuples

```python
from collections import namedtuple
City = namedtuple('City', 'name country population coordinates')
tokyo = City('Tokyo', 'JP', 36.933, (35.689722, 139.691667))
tokyo
```

```python
City(name='Tokyo', country='JP', population=36.933, coordinates=(35.689722, 139.691667))
```

```python
tokyo.population
```

```python
36.933
```

```python
tokyo.coordinates
```

```python
(35.689722, 139.691667)
```

Named tuple attributes

```python
City._fields
```

```python
('name', 'country', 'population', 'coordinates')
```

```python
LatLong = namedtuple('LatLong', 'lat long')
delhi_data = ('Delhi NCR', 'IN', 21.935, LatLong(28.613889, 77.208889))
delhi = City._make(delhi_data)
delhi._asdict()
```

```python
OrderedDict([('name', 'Delhi NCR'), ('country', 'IN'), ('population', 21.935), ('coordinates', LatLong(lat=28.613889, long=77.208889))])
```

You can also use \*.

```python
delhi = City(*delhi_data)
delhi
```

```python
City(name='Delhi NCR', country='IN', population=21.935, coordinates=LatLong(lat=28.613889, long=77.208889))
```

### Tuple as Immutable Lists

Tuple supports all list methods that do not involve adding or removing items, like

> s.\_\_iadd\_\_, s.\_\_append\_\_. s.clear(), s.copy(), s.\_\_delitem\_\_, s.\_\_insert\_\_, s.\_\_imul\_\_, s.\_\_reversed\_\_()

reversed(tuple) works without s.\_\_reversed\_\_. (list has it only for optimization)

## Slicing

## Using + and \* with Sequences

## Augmented Assignment with Sequences

## Managing Ordered Sequences with bisect

## When a List is Not the Answer
