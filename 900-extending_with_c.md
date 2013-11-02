# Extending with C

<br/>
[ Amit Kotlovski ](mailto:amit@amitkot.com) / [ @amitkot ](http://twitter.com/amitkot)



# Agenda

- Introduction
- Basic `ctypes` Example
- Explicitly using data types
- Detecting Errors
- Structures
- Pointers
- Appendix - Alternative Solutions



## Use Case

- __Legacy__ - Reuse existing libraries written in C
- __Performance__ - Replace part of our code with C



## Different Approaches

- Write C wrapping code to expose a Python interface
- Wrap C library inside Python
- Compile the Python code into C



## `ctypes`

- Wrapping C libraries using Python Code
- Relatively simple to use
- Included as part of the Python Standard Library



## Let's start simple

`examplelib.h`:
```c
void simplePrint(int a);
```

`examplelib.cpp`:
```c
void simplePrint(int a) {
    printf("a is %d\n", a);
}
```



## Using this library

```python
from ctypes import *

c = cdll.LoadLibrary('./libexamplelib.so')
c.simplePrint(30)
```
Output:
```
a is 30
```



## Loading C libraries

- Use `cdll.LoadLibrary()`
- In Windows can also `windll.LoadLibrary()` and `oldedll.LoadLibrary()`



## Calling Functions

- Automatic type conversion C ⇔ Python:
    - integer
    - string
    - Unicode string

- Otherwise - manually define type



## Function Arguments and Return Result

```c
double mult(double first, int second) {
    return first * second;
}
```

```python
from ctypes import cdll, c_double, c_int

mult = cdll.LoadLibrary("./examplelib.so")
mult.argtypes = [c_double, c_int]
mult.restype = c_double

print 'mult result is', mult(c_double(12.3), c_int(6))
```
```
mult result is 73.8
```



### Fundamental Data Types (1)

ctypes name    |C type    |Python type
--              |--         |--
None    |void    |None
c_bool    |C99 _Bool    |bool
c_byte    |signed char    |int
c_char    |signed char    |str
c_char_p    |char *    |str
c_double    |double    |float
c_float    |float    |float
c_int    |signed int    |int
c_long    |signed long    |long
c_longlong    |signed long long    |long
c_short    |signed short    |long



### Fundamental Data Types (2)

ctypes name    |C type    |Python type
--              |--         |--
c_ubyte    |unsigned char    |int
c_uint    |unsigned int    |int
c_ulong    |unsigned long    |long
c_ulonglong    |unsigned long long    |long
c_ushort    |unsigned short    |int
c_void_p    |void *    |int
c_wchar    |wchar_t    |unicode
c_wchar_p    |wchar_t *    |unicode



## Detecting Errors

We can set a callable Python object as restype to detect errors:

```python
def ValidHandle(value):
    if value == 0:
        raise WinError()
    return value

allow_even_numbers = c.allowEvenNumbers
allow_even_numbers.restype = ValidHandle
```
```python
>>> allow_even_numbers(4)
1
>>> allow_even_numbers(3)
Traceback (most recent call last):
  File "example.py", line 21, in <module>
    allow_even_numbers(3)
  File "example.py", line 14, in ValidHandle
    raise RuntimeError
RuntimeError
```



## Allocating Memory

```
int copy(char single, int count, char * out) {
    for (int i = 0; i < count; i++) {
        out[i] = single;
    }
    return 1;
}
```
```python
copy = c.copy
ch = c_char('*')
count = c_int(6)
s = create_string_buffer('\000' * 6)
copy(ch, count, s)
print 'copy result: ', s.value
```

```python
copy result:  ******
```



## Using Structures (C)

```c
typedef struct {
    int first;
    float second;
} ActualData;

ActualData sumData(const ActualData *data, int num) {
    ActualData result = {0, 0};
    for (int i = 0; i < num; i++) {
        result.first += data[i].first;
        result.second += data[i].second;
    }
    return result;
}
```



## Using Structures (Python)

```python
class ActualData(Structure):
    _fields_ = [('first', c_int),
                ('second', c_float)]

data = (ActualData * 3)((2, 0.1), (200, 0.5), (23, 1.2))
sum_data_func = c.sumData
sum_data_func.restype = ActualData
```
```python
>>> res = sum_data_func(data, len(data))
>>> print res.first, res.second
225 1.80000007153
```



## Pointers

`ctypes` pointers behave in a similar manner as C ones

```python
>>> from ctypes import *
>>> i = c_int(42)
>>> pi = pointer(i)
>>> pi.contents
c_long(42)
```



## Pointers

However, their pointed item is creates anew each time

```python
>>> pi.contents is i
False
>>> pi.contents is pi.contents
False
```



## Pointers

It's possible to switch the pointed value:

```python
>>> i = c_int(99)
>>> pi.contents = i
>>> pi.contents
c_long(99)

# The value is also accessable this way:
>>> pi[0]
99
```



# Summary

- `ctypes` allows us to access functions in C libraries
- Easy for ints and strings, not much harder using other data types



# Questions

![Questions](img/q_everything.jpg "Duncan Hull, https://secure.flickr.com/photos/dullhunk/202872717/")



## Appendix - Alternative Solutions
- [ Python.h ](http://docs.python.org/2/extending/extending.html) - Writing C
  wrapper code for using your libraries in Python
- [ SWIG ](http://www.swig.org/) - 3'rd party library for writing python
  Wrapping code
- [ Cython ](http://cython.org/) - Python superset language that can compile to C
- [ CFFI ](http://cffi.readthedocs.org/) - FFI wrapper, similar to ctypes



# Thanks!

[ Amit Kotlovski ](mailto:amit@amitkot.com) / [ @amitkot ](http://twitter.com/amitkot)
