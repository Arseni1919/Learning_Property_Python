# Python's `property()`

## Using property() as a Decorator

```python
# circle.py

class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):
        """The radius property."""
        print("Get radius")
        return self._radius

    @radius.setter
    def radius(self, value):
        print("Set radius")
        self._radius = value

    @radius.deleter
    def radius(self):
        print("Delete radius")
        del self._radius
```

```shell
>>> from circle import Circle

>>> circle = Circle(42.0)

>>> circle.radius
Get radius
42.0

>>> circle.radius = 100.0
Set radius
>>> circle.radius
Get radius
100.0

>>> del circle.radius
Delete radius
>>> circle.radius
Get radius
Traceback (most recent call last):
    ...
AttributeError: 'Circle' object has no attribute '_radius'

>>> help(circle)
Help on Circle in module __main__ object:

class Circle(builtins.object)
    ...
 |  radius
 |      The radius property.
```

## Providing Read-Only Attributes

```python
# point.py

class Point:
    def __init__(self, x, y):
        self._x = x
        self._y = y

    @property
    def x(self):
        return self._x

    @property
    def y(self):
        return self._y
```
```shell
>>> from point import Point

>>> point = Point(12, 5)

>>> # Read coordinates
>>> point.x
12
>>> point.y
5

>>> # Write coordinates
>>> point.x = 42
Traceback (most recent call last):
    ...
AttributeError: can't set attribute
```
```python
# point.py

class WriteCoordinateError(Exception):
    pass

class Point:
    def __init__(self, x, y):
        self._x = x
        self._y = y

    @property
    def x(self):
        return self._x

    @x.setter
    def x(self, value):
        raise WriteCoordinateError("x coordinate is read-only")

    @property
    def y(self):
        return self._y

    @y.setter
    def y(self, value):
        raise WriteCoordinateError("y coordinate is read-only")
```


## Creating Read-Write Attributes

```python
# circle.py

import math

class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def radius(self):
        return self._radius

    @radius.setter
    def radius(self, value):
        self._radius = float(value)

    @property
    def diameter(self):
        return self.radius * 2

    @diameter.setter
    def diameter(self, value):
        self.radius = value / 2
```
```shell
>>> from circle import Circle

>>> circle = Circle(42)
>>> circle.radius
42.0

>>> circle.diameter
84.0

>>> circle.diameter = 100
>>> circle.diameter
100.0

>>> circle.radius
50.0
```

## Validating Input Values

```python
# point.py

class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    @property
    def x(self):
        return self._x

    @x.setter
    def x(self, value):
        try:
            self._x = float(value)
            print("Validated!")
        except ValueError:
            raise ValueError('"x" must be a number') from None

    @property
    def y(self):
        return self._y

    @y.setter
    def y(self, value):
        try:
            self._y = float(value)
            print("Validated!")
        except ValueError:
            raise ValueError('"y" must be a number') from None
```
```shell
>>> from point import Point

>>> point = Point(12, 5)
Validated!
Validated!
>>> point.x
12.0
>>> point.y
5.0

>>> point.x = 42
Validated!
>>> point.x
42.0

>>> point.y = 100.0
Validated!
>>> point.y
100.0

>>> point.x = "one"
Traceback (most recent call last):
     ...
ValueError: "x" must be a number

>>> point.y = "1o"
Traceback (most recent call last):
    ...
ValueError: "y" must be a number
```

## Caching Computed Attributes

```python
# circle.py

from time import sleep

class Circle:
    def __init__(self, radius):
        self.radius = radius
        self._diameter = None

    @property
    def diameter(self):
        if self._diameter is None:
            sleep(0.5)  # Simulate a costly computation
            self._diameter = self.radius * 2
        return self._diameter
```
```shell
>>> from circle import Circle

>>> circle = Circle(42.0)
>>> circle.radius
42.0

>>> circle.diameter  # With delay
84.0
>>> circle.diameter  # Without delay
84.0

>>> circle.radius = 100.0
>>> circle.diameter  # Wrong diameter
84.0
```


## Credits

- [Python's property(): Add Managed Attributes to Your Classes](https://realpython.com/python-property/#:~:text=Python's%20property()%20is%20the,use%20it%20without%20importing%20anything.)