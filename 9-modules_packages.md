# Modules and Packages

<br/>
[ Amit Kotlovski ](mailto:amit@amitkot.com) / [ @amitkot ](http://twitter.com/amitkot)



# Agenda
- What is a Module
- Loading a Module
- Running a module as a script
- What is a Package
- Loading a Module from a Package
- Selected Modules Overview



## What is a Module

- Every python file is a module



## Loading a Module

example.py:

```python
def mult(first, second):
    return first * second
def welcome(name):
    return 'Have a wonderful day, {}!'.format(name)
```

Using the interpreter:
```python
>>> import example
>>> print example.mult(3, 5)
15
```



## Importing into our namespace

We can import a function or class directly into our namespace
```python
>>> from example import mult, welcome
>>> mult(20, 6)
120
>>> welcome('Griselda')
'Have a wonderful day, Griselda!'
```



## Importing everything from a module

We can import everything(\*) from a module directly into our namespace
```python
>>> from example import *
>>> mult(4, 21)
42
>>> welcome('Kilikina')
'Have a wonderful day, Kilikina!'
```

(\*) except items whose name start with \_ , e.g. \_hidden()



## `from foo import *`

- ↑ Easy to type the import line (imports everything)
- ↑ Easier to use (no need to type the namespace)
- ⬇ Imported names and local ones might clash



## Running a module as a script

We add these lines to modules:
```python
if __name__ == '__main__':
    # main code of the script
```

What does it do?



## What's in a `__name__`?

- A variable that is defined in each module
- Usually holds the module's name (e.g., `'example'`)
- When the module is being run as a script, `__name__` holds the string `'__main__'`
- Unless we use this condition, our main code will run whenever the module is imported



## What is a Package

- A directory containing modules



## The Package structure

The package directory structure:
```
furniture/
        __init__.py
		tables/
				__init__.py
				livingroomtable.py
				coffeetable.py
		chairs/
				__init__.py
				armchair.py
				electricchair.py
```



## Loading a module from a Package

```python
import furniture.tables.livingroomtable
print furniture.tables.livingroomtable.get_height()
```
avoid using the package namespace:
```python
from furniture.tables import livingroomtable
print livingroomtable.get_height()
```
if we only need a single method:
```python
from furniture.tables.livingroomtable import get_height()
print get_height()
```



## `import *` with Packages

Imports everything listed under the variable `__all__` defined in the file
\_\_init\_\_.py at the root of the directory

<br/>
furniture/tables/\_\_init\_\_.py:
```python
__all__ = ["livingroomtable", "coffee"]
```
```python
>>> from furniture.tables import *
```



# Selected Modules Overview



## PySerial

- Easy Python code access to devices connected to the Serial port
- Supports a multitude of configurations including different byte sizes, stop
  bits, parity etc.
- Allows accessing the device like a file
- 3'rd Party module



## PySerial - Common Usage

Accessing a device using 9600 baud, 8-N-1 (8 data bits, No parity bit and 1
stop bit)
```python
>>> import serial
>>> ser = serial.Serial(0)  # open first serial port
>>> print ser.name          # check which port was really used
>>> ser.write("hello")      # write a string
>>> ser.close()             # close port
```



## PySerial - Common Usage (2)

Accessing a device on port 1 using 19200 baud, 8-N-1 with a timeout of 1 second
```python
>>> ser = serial.Serial('/dev/ttyS1', 19200, timeout=1)
>>> x = ser.read()          # read one byte
>>> s = ser.read(10)        # read up to ten bytes (timeout)
>>> line = ser.readline()   # read a '\n' terminated line
>>> ser.close()

```



## `subprocess`

- Used for spawning new processes and shell commands
- Allows passing and receiving data from the child processes' pipes
- A part of the standard library



## Common Usage

Run a command with argument, wait for it to end and receive the return code:
```python
>>> res = subprocess.call(['which', 'sh'])
/bin/sh
>>> res
0
```

Run the command, wait for it to end and return its output as a byte string:
```python
>>> res = subprocess.check_output(['which', 'sh'])
>>> res
'/bin/sh\n'
```



## More `subprocess` usage
Run the command using the shell, _not_ waiting for it, but rather returning
pipes to all three streams:
```python
p = Popen("cmd", shell=True, bufsize=bufsize,
          stdin=PIPE, stdout=PIPE, stderr=PIPE, close_fds=True)
(child_stdin,
 child_stdout,
 child_stderr) = (p.stdin, p.stdout, p.stderr)
```



## `optparse`

- Builds a parser for the command line arguments passed to a script
- Simple yet powerful API for describing optional and mandatory arguments
- Builds usage and help messages on its own
- A part of the Python standard library
- Starting Python 2.7 this module is being replaced by `argparse`



## Usage Example

t.py:
```python
from optparse import OptionParser
[...]
parser = OptionParser()
parser.add_option("-f", "--file", dest="filename",
                  help="write report to FILE", metavar="FILE")
parser.add_option("-q", "--quiet",
                  action="store_false", dest="verbose", default=True,
                  help="don't print status messages to stdout")

(options, args) = parser.parse_args()
print 'options are', options
print 'args are', args
```



## Running it

```sh
$ python t.py --help
Usage: t.py [options]

Options:
  -h, --help            show this help message and exit
  -f FILE, --file=FILE  write report to FILE
  -q, --quiet           don't print status messages to stdout
```
```sh
$ python t.py -f banana_file.txt fruits vegetables books
options are {'verbose': True, 'filename': 'banana_file.txt'}
args are ['fruits', 'vegetables', 'books']
```



## Adding Options

Argument    | Meaning                           | Example
---         | ---                               | ---
short       | Short option                      | "-f"
long        | Long option                       | "--file"
action      | How to use the argument           | action="store"
type        | Type of data of the argument      | type="string"
dest        | Variable the argument is saved to | dest="filename"
default     | Default value                     | default="default_file" or leave out for None
help        | Help explanation for this option  | help="Name of the file to be used"

```python
parser.add_option("-q", "--quiet", action="store_false", 
                  dest="verbose", default=True)
```



## Main Action Types

Argument        | Meaning
---             | ---
`store`         | Store the option's argument (default)
`store_true`    | Store a true value
`store_false`   | Store a false value

See more at [The Python Documentation](http://docs.python.org/2/library/optparse.html#defining-options)



# Summary

- Every Python file is a module
- A Package is a directory of Python files with an \_\_init\_\_.py file in it
- Modules are imported with their (optional) namespace
- Python has many built in and 3'rd Party libraries for solving common problems



# Questions

![Questions](img/q_everything.jpg "Duncan Hull, https://secure.flickr.com/photos/dullhunk/202872717/")



# Thanks!

[ Amit Kotlovski ](mailto:amit@amitkot.com) / [ @amitkot ](http://twitter.com/amitkot)
