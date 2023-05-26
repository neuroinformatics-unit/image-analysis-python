
# Setting Up Python For Analysis

## Working Interactively: how?

How do actually develop code in Python? Practically speaking, what is the development cycle? If you are developing an application or tool that you intend to share then it makes sense to:

* Plan this from the start what the layout will be and begin working on lower level stuff first.
* Create a pip-installable package with tests.
* Start writing the basic modules.
* Write tests for these basic modules.
* `pip install -e .` your package
* Do test-based development at the command line. 

This is perhaps the "correct" way to do things but it seems excessive if, for example, you have a small dataset (possibly a one-off) and you want to load it up, extract some features, and see whether things have worked out as expected. The process for doing this sort of thing in MATLAB works very well. The process is:

* Start typing text in script (shares base workspace scope) or function file (local scope).
* Run it at the command line and see if it works. It may spawn figures as needed. 
* Iterate: fixing bugs or adding features and re-run.
* As appropriate you might create additional files or functions in the MATLAB path.  

In MATLAB doing this is very easy because scripts and functions are always run using the latest saved version. Even running instances of classes modify their behavior if the source code is changed. Adding functions to the MATLAB path makes them available any time. Doing things of this sort in Python is surprisingly non-obvious and some of them may be impossible. You will run into the following issues:


* It is not obvious how to run a script. You need to know how to do it in iPython or you need to use Spyder.
* If you make a module and import it in iPython but then alter it, the alterations are not available. If you don't know better you will quit and restart iPython. 
* Figures do not appear until `plt.show()` is called and when that is called the CLI is blocked.
* It is not obvious where to put your "quick & dirty" modules. Is there an equivalent of the MATLAB path?



## Are Jupyter notebooks the answer?
At first glance Jupyter notebooks seem like a good option. The allow you to see your workflow. Things can be re-run easily by pressing the run button. Results are saved. 
The issue, however, is that for any substantial analysis you will need to write potentially large numbers of functions (or even multiple packages) and this isn't part of the Jupyter workflow. You will learn no software dev skills if you try sticking with Jupyter. Jupyter is good if you want to summarize results at a very high level at the end, use it a notebook for progress reports, etc. It does not answer the issues above.  


## What about Spyder?
The most straightforward option is to try Spyder. This solves most of the issues above and also gives you things like a variable explorer, a debugger, and a linter. There is a lot to be said for it and you should definitely check it out and look at some tutorials. However, Spyder may not be the best solution: you may not always be able to use it (e.g. logged in remotely via SSH) or you may not like the interface. Perhaps you prefer a different text editor or IDE? (We won't discuss pyCharm here, as it's quite complicated and a lot of the analysis features are not free, but you can learn more about it [here](https://www.youtube.com/watch?v=46RjXawJQgg&ab_channel=JetBrains)). 

A lot of what Spyder does seems like black magic at first. How are modules reloading automatically, for instance? The solution we look at here is how to tweak iPython manually so it provides the key features you need to do effective interactive analysis. 


## Using iPython as a MATLAB-like working environment

### The magic of iPython

iPython has a whole load of [magic commands](https://ipython.readthedocs.io/en/stable/interactive/magics.html) that start with a `%` and help it behave as a nice interactive editor. Let's look a few. Start iPython and try the commands for checking the current working directory and changing it. 

```
In [1]: %pwd
Out[1]: '/Users/bob/Desktop'

In [2]: %cd ../
/Users/bob/
```



### Running scripts in iPython

The most basic thing you need to do is run scripts in iPython. A "script" is plain text `.py` file that contains a bunch of commands that you want to run in series. A script is not a module. Fire up your favourite text editor and make a script somewhere (the Desktop is fine). The contents of my script (`bob.py`) looks like this:

```
print("I am running the script")
t = 10
t2 = t**2
```

You can run the script using either of the following approaches

```
In [1]: %cd /Users/bob/Desktop
Out[1]: %run bob
I am running the script
```

or

```
Out[1]: %run  /Users/bob/Desktop/bob
I am running the script
```

The variables (`t` and `t2`) created by the script are placed in the base workspace of iPython and we can see them as follows

```
In [2]: %whos
Variable   Type    Data/Info
----------------------------
t          int     10
t2         int     100
```

If you edit your script and re-run it as above then the updated version will run. Let's try the same thing with a simple module.

### Developing modules interactively with iPython

Modify your `bob.py` file so it contains a couple of methods:

```python
def intro():
    print("I am running the script")
    t = myVar()
    t2 = t**2
    print(t2)

def myVar():
    return 10
```

In iPython ensure your working directory is the same as that of the module and try importing and running it.

```
In [1]: %pwd
Out[1]: '/Users/rob/Desktop'

In [2]: import bob

In [3]: bob.intro()
I am running the script
100
```

 But! If you edit `bob.py` so that `myVar` so it returns a different number, this will not be implemented if your re-run `bob.intro()` and even if you run `import bob` again, that will not help. The solution is to enable module auto-reloading. Close iPython and restart it then do the following.

```
In [1]: %load_ext autoreload

In [2]: %autoreload 2

In [3]: import bob

In [4]: bob.intro()
```

Try editing the return value of `myVar` or make any other changes you fancy. When you run `bob.intro()` the changes take effect. This is great! It also works with other modules, so if you have imported multiple things that depend on each other all are reloaded each time. 

In the case above we are importing `bob` because it is in the current working directory. What if we want to import modules in other directories? Of course in many (most?) cases we will be importing things that have been installed using `pip` (perhaps with `pip -e`) but this is not always appropriate. When you are slowly developing analysis pipelines you might not want to immediately create pip-installable packages. What to do in this case? Python looks in the list `sys.path` and imports modules it finds there. So let's say we have our `bob.py` on the Desktop but we have a different working directory and we want to import it. This is how we can do that:

```
In [1]: %pwd
Out[1]: '/Users/bob/Dropbox/work/development'

In [2]: sys.path.append('/Users/bob/Desktop')

In [3]: import bob

In [4]: bob.intro()
I am running the script
100
```

The above works because `sys.path` is just a list that we can add to as we wish. You can build up a list of useful paths and add them each time iPython opens using a startup file (see below). **Note**: as projects mature and become more long term it makes sense to transition them to more organised packages that can be installed using `pip` and have systematic testing, etc. 

## But I liked Jupyter notebooks!
You can have both!
Start Jupyter and do some stuff then at the bash CLI:
```
 jupyter console --existing
```

The `%connect_info` typed into Jupyter Notebook will help you connect to it if there are multiple running notebooks.


## jupyter qtconsole
```
pip install pyqt5-tools
```

Then
```
jupyter console
```

The `%qtconsole` magic spawns a new terminal window with the same kernel.

### Customizing iPython

This works well so let's make the change permanent. We don't want to have to type in this autoreload stuff every time we start iPython. We will edit the `python_config.py` file. To locate the file type `ipython locate profile default` in the system (not iPython) prompt. On Unix-like systems it should be at  `~/.ipython/profile_default/` and the iPython config file should be at `~/.ipython/profile_default/ipython_config.py` If it is missing you can make it with `ipython profile create` 

Open this file in your favourite editor. Find the following two settings, edit and uncomment the lines so that the read:

```
c.InteractiveShellApp.extensions = ['autoreload']     
c.InteractiveShellApp.exec_lines = ['%autoreload 2']
```

Each time iPython starts it will have module reloading enabled. If you wish to block reload of large modules that might take time you can do it as follows

```
c.InteractiveShellApp.exec_lines = ['%autoreload 2','aimport - numpy scipy']
```

You can verify that worked after starting iPython:

```python

In [1]: %aimport
Modules to reload:
all-except-skipped

Modules to skip:
numpy scipy
```

The `%edit` magic is used to open files in an editor. You can choose your editor by modifying the config file. The following change sets it to use SublimeText

```python
c.TerminalInteractiveShell.editor = 'subl'
```



### The iPython startup file

You can run arbitrary Python code each time iPython starts up. Create a `.py` file in `~/.ipython/profile_default/startup` and edit it. The following `~/.ipython/profile_default/startup/00_mystartup.py`, for instance, imports a bunch of commonly used tools. 

```python
print('Running startup file')

# Do common imports 
import os
import sys
import numpy as np
import matplotlib.pyplot as plt
```






## Plotting

### Making matplotlib usable in iPython

The first problem you will have when making plots is that they don't appear unless you run `plt.show()` and then their presence blocks the command line. e.g.

```python
In [10]: y = np.random.rand(1,100)[0]

In [11]: plt.plot(y)

Out[11]: [<matplotlib.lines.Line2D at 0x11fe5cd90>]

# Where is my plot?

In [12]: plt.show()

# Ah, there it is! But not I can't type anything until I close it. 
```

To fix this turn on interactive plotting: `plt.ion()` (you can disable it with `plt.ioff()`). You can place `plt.ion()` in your startup file so it's always present by default. 



### Working with multiple figure windows

The basic way of making a figure is this

```python
plt.plot(xData, yData, '-k')
plt.grid(True)
```

But if we want to make multiple figures something like this is needed:

```python
# Let's make two figures:

# The first...
fig1 = plt.figure()
ax1 = fig1.add_subplot(1,1,1)
ax1.plot(y)
ax1.grid()

# ...and the second
fig2 = plt.figure()
ax2 = fig2.add_subplot(1,1,1)
ax2.plot(y)
ax2.grid()
```



You can simplify the figure creation into one line as follows:


```python
fig1, ax1 = plt.subplots()
ax1.plot(y)
```

When working interactively with multiple figures it easy to end up with too many figure windows everywhere or to over-write an important existing figure with new data. The ability to label figures comes to the rescue. For example:

```python
# Make two figures with different labels and don't save their instances as a variable
plt.figure('my_label_01')
plt.figure('my_label_02')

# We can generate a list of available labels:
plt.get_figlabels()
 ['my_label_01', 'my_label_02']

# Add an axis and random data data to the first figure
fig = plt.figure('my_label_01') # Does not create a new figure but returns the original window
ax = fig.add_subplot(1,1,1)

```

 It makes sense to always assign a label to a figure whenever you make one. This way when you iterate in your development process you are not generating new figure windows each time or overwriting existing figures. Run `fig1.clf()` to clear existing data in the figure window.
 Labels are managed as follows:

```
# Labels in existing plots
In [30]: fig.get_label()
Out[30]: 'my_label_01'

In [31]: fig.set_label('newlabel')



```

NOTE: this dev cycle works better
```python
fig = plt.figure('bboxes')
fig.clf()
axs = fig.subplots(1,2)
```


## Other odds and ends

### Get rid of the extra space between lines

If you don't like the newline between prompt lines you can get rid of it as follows:

```python
from IPython.terminal.prompts import ClassicPrompts
ip = get_ipython()
ip.separate_in = '' # No spaces between lines
```

Of course that can go into your iPython startup script.



### Running system commands from within iPython

```
 !find . -name '*.py'
```

### Getting help
e.g.
```
 help(plt.plot)
```


### Quick benchmarking

The following magic iPython commands can be used for benchmarking

* `%time`
* `%timeit`
* `%run -p my_script.py`
