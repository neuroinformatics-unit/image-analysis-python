# Interactive data analysis in Python


## How to give the presentation

You will want to actually do most of the things described in the slides otherwise
it will be confusing, boring, and the information will be presented too quickly. 

You will need to have `ipython`, `numpy`, `matplotlib`, `jupyter-console`, and `jupyter` installed. 



## Setting up
If you normally have autoreload enabled, disable it with `%autoreload 0` at the iPython prompt. 
Run `plt.ioff()` too, as that's the default. 


## Examples
The following is a condensed summary of what is in the presentation. 
You can print this out if needed so you don't need to jump back and forth between the presentation and the iPython prompt. 
The presentation is detailed enough that people probably do not need notes and can just follow along with the code or listen. 


### Introduce what iPython is
You just type code and it runs
```
import numpy as np
np.sin(np.pi)
```
### Magics
1. `cd`, `%cd`, `pwd`, `%pwd`
2. `%automagic true` and `%automagic false`
3. Use `find` via `!find`

### Simple script
1. Make script with simple stuff in it.
2. `%run` or `run` the script.
3. Show the variables are in workspace with `whos`
4. Show that you can get help: `help plt.plot`


### Benchmarking
```
In [11]: time np.random.rand(1000);
CPU times: user 30 µs, sys: 0 ns, total: 30 µs
Wall time: 64.4 µs
In [12]: timeit np.random.rand(1000);
5.42 µs ± 106 ns per loop (mean ± std. dev. of 7 runs, 100,000 loops each)
```

### Garbage collecting
Needed because iPython hold references to stuff so variables may persist even after `del`
```python
import gc
gc.collect()
```


### Plotting Interactively
Show the annoying thing:
```
In [24]: plt.plot(np.random.rand(1000))
Out[24]: [<matplotlib.lines.Line2D at 0x7f164aaedaf0>]
# NO PLOT!
In [25]: plt.show()
In [26]: # BLOCKED CLI!
```

Repeat after `plt.ion()`


### Multiple plots

Make a plot
```python
x = np.arange(0,100)
y = x*2 + np.random.rand(len(x))*3;
plt.plot(x,y,'ok')
```

modify it

```python
plt.grid
```

This will overlay data:

```python
y2 = x*3 + np.random.rand(len(x))*10;
plt.plot(x,y2,'+r')
```

To plot in a new window:
```python
plt.figure()
plt.plot(x,y2,'+r')
```


### Avoiding multiple plots everywhere
Let's modify the script so it reads something likes
(note we must place imports here)
```python
import matplotlib.pyplot as plt
import numpy as np

print("hello, people")

t=123;
m=20;

print(t*m)



plt.figure()
x = np.arange(1,100)
y = x*2 + np.random.rand(len(x))*2
plt.plot(x,y,'or')

plt.figure()
x = np.arange(1,100)
y = x*6 + np.random.rand(len(x))*6
plt.plot(x,y,'+g')
```
Run it a few times. Annoying. 

```
plt.close("all")
```

Let's make less annoying by adding labels to figures: 
```python
plt.figure("fig1") #reuse this 
plt.clf() # but wipe previous data
```

Run repeatedly. Makes the point nicely how much better this is.

Labels are properties of the figure and can be manipulated:

```python
f1 = plt.figure()
f1.get_label()
f1.set_label('mylabel')
f1.get_label()
```

### Module auto-reloading
Convert you test function into a module so it is something like this. 
Here I called my function `BOB.py` and it has:

```python
import matplotlib.pyplot as plt
import numpy as np


def intro():
    print("hello, people")

    t=123;
    m=20;

    print(t*m)


def plotter():
    plt.figure("A")
    plt.clf()
    x = np.arange(1,100)
    y = x*2 + np.random.rand(len(x))*2
    plt.plot(x,y,'or')
```

Then in iPython we can do:
```python
import BOB
BOB.intro()
BOB.plot()
```

Make changes to the module and re-run. The changes have not been recognized.
Fire up autoreloading and show that changes will now be recognised
```
%load_ext autoreload
%autoreload 2
```

Hammer home why this is very useful. 
It works with modules you are developing and have installed with `pip -e .`


### Class auto-reloading
Now convert to a class and demo how this works with classes. 
For example:

```python
class MYCLASS():

    def __init__(self):
        self.stuff=123
        self.M = 40


    def mult(self):
        print(self.stuff * self.M)
```

Then in iPython
```
from BOB import MYCLASS
M = MYCLASS()
M.mult()

```
Now modify `mult` or add a method. Show how changes are updated. 
Emphasise why this is even more useful than re-importing a module. 
Note that the constructor is not re-run each time. 



### The python path
We have been running a script in the current directory. If we change directory the auto-reloading will fail. The list of searched directories is in `sys.path`. 
If we add the current directory to it, then we can change directory at will:

```path
t = %pwd
sys.path.append(t)
```

Show this works.
Emphasize that `sys.path` is just a list. 


### Customizing iPython
We can set up iPython to start with nice settings. 
Find the config file with `ipython locate profile default` and if needed make with 
`ipython locate profile default`

Change lines in `ipython_config.py` to:

```python
c.InteractiveShellApp.exec_lines = ['%autoreload 2',
                                    'aimport - numpy scipy']
c.InteractiveShellApp.extensions = ['autoreload']
```

Can verify this worked at iPython prompt with
```
%aimport
```

Can choose your own editor
```
c.TerminalInteractiveShell.editor = 'subl'
```

You can automatically import stuff and so on:
Create: `~/.ipython/profile_default/startup/00_mystartup.py`

```python
print('Running startup file')
# Do common imports
import os
import sys
import numpy as np
import matplotlib.pyplot as plt
plt.ion()
```

### More customizations
The properties of the currently running iPython instance can be obtained with `get_ipython()`.
Then we can change them.
e.g. to remove spacing between lines:

```python
ip = get_ipython()
ip.separate_in = '' # No spaces between lines
```

### Jupyter and iPython
Start Jupyter at the system command line

```
$ jupyter notebook
```

Now create a new notebook and do some random stuff in it. 

Make sure you have `pip install jupyter-console`

then:
```
$ jupyter console --existing
```

We have access to the notebook!
```
In [1]: whos
Variable   Type       Data/Info
-------------------------------
x          ndarray    1000: 1000 elems, type `float64`, 8000 bytes

```

Prove this by altering the value of `x` (or whatever your data are) in the iPython console and plotting in Jupyter. 


--

## The Presentation Document
The presentation is made in [reveal.js](https://revealjs.com/demo).
[Read the documentation](https://revealjs.com/markup/)
Write HTML code interactively with [phcode.dev](https://phcode.dev/).
