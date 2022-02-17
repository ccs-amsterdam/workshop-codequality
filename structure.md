# Code structure

Consider the 'anti-pattern' wordle code in [eldrow.py](https://github.com/vanatteveldt/eldrow/blob/main/eldrow.py).
The program works, and the game is enjoyable, but the code is hard to read and maintain or reuse.
This is partly due to the **structure** of the code. 

In particular, the current code is almost entirely "imperative", meaning it contains commands that are executed in sequence.
For larger projects, this is often not optimal. 

In python (and equivalently in many other languages), the main ways to structure code is using *functions* and *classes*
This tutorial will not go into the basics of how to write a function or class. 
Rather, we will discuss when and in what way to use functions and classes to make code readable.

There are three common best practices that are often quoted for structuring code: *Don't repeat yourself (DRY)*, *separation of concerns*,
and *You ain't gonna need it (YAGNI)*. 

## DRY: Don't repeat yourself

The most important and obvious idea behind using functions is to avoid copy-pasting code, aka repeating yourself.
Quite often, you find yourself doing the same thing in multiple places in the code.

**What:** A very obvious example is copy-pasting code to repeat the same operation on multiple datasets, values, rows or columns. 
Rather than copy-pasting the operation, it is almost always better to turn it into some form of *for loop*. 
In most cases, but especially if the operation is non-trivial and/or occurs in separate locations of your code,
it can also be a good idea to place the operation in a separate *function*. 

**How: loops** Let's look at lines 29-53 of the 'eldrow' code: https://github.com/vanatteveldt/eldrow/blob/main/eldrow.py#L29-L53

Here you see the same operation (evaluating one letter from the guess) repeated five times. As a first step, we can transform this into a for loop:

```{python}
for i in range(len(guess)):
    if guess[i] == word[i]:
        output.append(Back.GREEN)
    elif guess[i] in word:
        output.append(Back.YELLOW)
    output.append(guess[i])
    output.append(Style.RESET_ALL)
```

As you can see, this is simply the repeated code block with the concrete number (1 -- 5) replaced by the variable `i`. 
In general, this process of taking 'concrete' code and turning it into a more abstract 'template' is called *abstraction*. 

**How: functions** A next step that can be made is to move the repeated code into a seperate function.
As you know, a function is a block of code that can take specific parameters and that can be called from anywhere in your code.
For the code above, this would look like:

```{python}
# function definition at the top of the script:
def evaluate_character(output, word, guess, index):
    if guess[index] == word[index]:
        output.append(Back.GREEN)
    elif guess[index] in word:
        output.append(Back.YELLOW)
    output.append(guess[index])
    output.append(Style.RESET_ALL)
    
# ... in the main loop ....
for i in range(len(guess)):
    evaluate_character(output, word, guess, i)
```

In this case, the direct benefit of turning the loop body into a function is not immediately clear.
However, this is very useful if the function can be used in different places of the code.
For example, if you need to validate a word or clean a text in multiple places in your program. 

But also in a case like above, moving the code into a separate function can help for readability, 
as it decreases the complexity of the main loop, making it easier to get a quick overview of what happens in each step,
and being able to zoom into the specifics by looking at the function definition
(which is generally made easy in PyCharm or other editors as you can click or right-click on the function name to see the definition).
Moreover, the function makes the *dependencies* between blocks of code clear, since the dependencies are included in the parameters. 

That said, if this was our only change it would probably not help much in terms of reusability or even readability.
However, it is a good first step to make more improvements as will be discussed in the next block. 

Note that if you have slightly different versions of the same operation, you can still use this pattern by using a parameter to switch between the versions.
For example, if we would sometimes want to use the `evaluate_character` function without outputting the final reset character we could make something like:

```{python}
def evaluate_character(output, word, guess, index, reset=True):
   # ... start with the same code as above ...
   if reset:
       output.append(Style.RESET_ALL)
```

And then call it like:

```{python}
# with reset:
evaluate_character(output, word, guess, i, reset=True)
# without reset:
evaluate_character(output, word, guess, i, reset=False)
# default behaviour: with reset
evaluate_character(output, word, guess, i)
```

(there is no need to include `reset=` in the code, but for this kind of options it makes the code a lot more readable to have `reset=True` rather than just `True`.
In general, using named arguments for all optional parameters is usually a good idea).

**Why:** The main reason for avoiding repeated code is maintainability. 
If you have the same code repeated five times, and you want to correct a mistake or change something,
there is a good chance that you might miss some instances of the repeated code, especially if the operation occurs in multiple different places.
This would lead to inconsistent behaviour and later it would not always be clear why there are two versions of the operation and which is the correct version.
By *abstracting* the operation to a function which is called from multiple locations, you avoid repeating yourself and make the code more future proof. 

## Separation of concerns: do one thing, and do it well

**What:** A second principle of writing good code is separation of concerns:
try to make sure each function or block of code does one thing (and does that one thing well).
This is very similar to writing papers: each paragraph should really make one point (and hopefully make that point well),
and each section should have one clear purpose.

In code, that generally means that a function should not mix e.g. *business logic* (e.g. when is a word valid) and *presentation logic* (how to present this to the user). Similarly, code that deals with external data source (files or databases) should generally be separate from code that does analyses on these data. 
Code should also not mix different kinds of business logic unless they are related.  

**How:** 
Let's look at the function we wrote above. This clearly mixes two concerns: determining the state of a letter (correct, in word, or incorrect) and outputting a color. By changing this into two separate functions, it will be easier to see that the 'business logic' is correct and makes it more explicit how it relates to the presentation logic:

```{python}
def evaluate_character(word, guess, index):
    if guess[index] == word[index]:
        return "CORRECT"
    if guess[index] in word:
        return "INWORD"
    return "INCORRECT"

COLORS = {"CORRECT": Back.GREEN, "INWORD": Back.YELLOW, "INCORRECT": ""}
def color_code_characater(character, evaluation):
    color = COLORS[evaluation]
    return f"{color}{character}{Style.RESET_ALL}"

# ... and in the main loop ....
for i in range(len(guess)):
    evaluation = evaluate_character(word, guess, i)
    coded_char = color_code_character(guess[i], evaluation)
    output.append(coded_char)
```

As you can see here, we now have two functions that have a single purpose (evaluating or color-coding),
and the dependency between the functions is clear (evaluating requires the word, guess, and letter index,
while color coding requires only the character and the evaluation). 
In my opinion, this also makes the main loop more legible, as you can immediately see that it first does evaluation, 
then color-codes the word, and finally appends it to the output. 

To understand the main logic, you don't even need to read the function definitions,
but when needed you can 'zoom in' to the specific implementation by reading those definitions. 
Moreover, if something is wrong with either the validation or the color coding, or if you want to change the way either part works, 
you now immedately know where to look.

**Why:** As for DRY, the goal of this principle is to make code maintainable and readable. 
If each function does exactly one thing, it is easy to check (and to test and document) that one function.
It also makes it easier to make changes later on: if you e.g. want to move from a terminal-wordle to a browser-based worlde,
you would ideally only have to change the presentation logic and keep the business logic of validating guesses the same. 

## YAGNI: You ain't gonna need it

**What:** When writing functions or other code that you think other people (or you) will use,
it is tempting to think about all possible use cases and make your code as widely applicable as possible.
Although this sounds noble, our advise here is to focus on what you need right now, not on what you might need later.
The reason for this is that *you ain't gonna need it (YAGNI)*, by which we mean that many of the use cases you envision now won't actually occur,
if they do occcur they probably have slightly different requirements than you thought, and you probably didn't envision the most frequent future use cases.

So, rather than immediately adding features or options for things that you might need later,
focus on doing what you need done now.
By writing that code in a clean way (through abstraction and separation of concerns) it should be relatively easy to add features later,
when you actually need the features and most importantly when you understand exactly what is needed.

**Why:** While it seems to make sense to make a program as useful as possible, adding features always comes at a cost.
Your code becomes more complex, and once you add a feature it becomes a sort of 'promise' or obligation to keep that feature maintained.
This might actually make it more difficult later to properly implement the feature once you understand the exact requirements, as then 
you have to keep both the old version and the 'proper' version in place. 

## Modules and packages

*Namespaces are one honking great idea -- let's do more of those!* (the Zen of Python)

**What:** If you think of a function as a paragraph, classes, modules and packages can be seen as sections or chapters.
In programming lingo, these larger units are called *namespaces*, as they contain the names of various functions and variables. 
By separating functions in different namespaces you can make the larger organization of your code more explicit (increasing readability),
and prevent clashes where two pieces of code accidentally use the same name.

**How: Modules and importing:** Modules are relatively easy to use: Every python file is automatically a module, packaging the functions or other code inside it. 
To use functions from another module, you need to `import` them first, either by importing the whole module or by importing specific names from the module:

```{python}
# file: database.py
def init():
    do_something_smart()
    
# file: anotherfile.py
import database
database.init()
# Or:
from database import init
init()
```

Our advice would be to generally use the first style (`import database`) unless you use that module in a lot of places in your code.
The reason for this is explicitness: if you see `database.init()` in the code, you immediately know what's happening,
while just `init()` probably means you need to scroll back up and check from what module the function came.

Even stronger we would really advice against (almost) ever using `from database import *`, because it is makes it very unclear
where later functions come from, and is error-prone if the contents of the importer module change. 

**How: Packages:** Like any python file is a module, any folder (directory) containing python files is a *package*.
You can test this by creating a folder containing a python file, and then using that from *outside that folder*:

```{python}
# file: database/customer.py
def find_customer(id):
  do_something()
  
# file: test.py
import database.customer
c = database.customer.find_customer(1)
# --or:--
from database import customer
c = customer.find_customer(1)
# --or:--
from database.customer import find_customer
c = find_customer(1)
```

As above, you can decide to import the package, the module, or the function directly into your namespace.
In general, the middle option is probably the best balance between consiseness and explicitness, but that can certainly vary by usage.
For example, since the function here is called `find_customer` it makes it more acceptable to directly import the function,
while if the function was simply named `find` it would be obvious to keep it as `customer.find()` for the sake of readability. 

Note that there are two special files that you can have in every package: `__init__.py` and `__main__.py`
(in fact, until python 3.3 it was required to have an `__init__.py` file in every package).
The `__init__.py` file contains code that is executed whenever the package itself is imported. 
The main function of this file is to import functions (or other names) from one of the modules in the package directly into the package,
so the user can directly use those functions from the package:

```{python}
# file: database/customer.py
def find_customer(id):
  do_something()

# file: database/__init__.py
from .customer import find_customer
  
# file: test.py
import database
c = database.find_customer(1)
```

Try this yourself by adding an `__init__.py` file in the module you created!

(Note the use of the relative import `from .customer` to import a module from the same package)

**When/Why:** As your project grows bigger, it is useful to organize related groups of functions into larger building blocks or *namespaces*.
Leading here is again separation of concerns: it could e.g. make sense to have one module for input/output and a separate module for the main algorithm.
In the end, however, often these choices are also stylistic in the sense that there is often not one clear and obvious best way to organize code.
In general, we would warn against overcomplicating your structure as this can actually make code less readable, in the same sense that a 2,000 word essay should probably not be divided into 3 chapters with 4 sections each. 

For example, for the new [amcat4](https://github.com/ccs-amsterdam/amcat4) backend we now have only two packages 
(`amcat4` itself to deal with most business logic, and `amcat4.api` to provide the REST API),
and about 10 modules in total (e.g. `amcat4.elastic` to deal with talking to elastic, and `amcat4.api.query` contains the API endpoints related to querying).

### __main__: Command line scripts

**What:** Very often, code doubles as a library (intended to be imported) and a command line script (intended to be run from the command line / terminal).
Offering a command line interface is often a useful way for others to easily discover or use your code,
or for running administrative actions on e.g. a database system.
However, you don't want the command line actions to be run when someone imports your code as a module, so it's important to keep those actions separate. 

**How:** There are two mechanisms to separate library code from command-line actions. 
Within a module, you can use the `__name__ == '__main__' fence:

```{python}
print("This code is always executed on import")
if __name__ == '__main__':
    print("This code is only executed if called from the command line")
```

Try this out by creating a file like above, and doing both `import myfile` within an interactive python shell and running `python myfile.py` from your terminal.

Very often, the code above the guard will contain a number of functions, while the command line code will 
use [argparse](https://docs.python.org/3/library/argparse.html) to parse command line arguments and then run one or more of those functions:

```
def find_customer(id):
    return do_something(id)

if __name__ == '__main__':
    parser = argparse.ArgumentParser(description=__doc__)
    parser.add_argument("customer_id", type=int, help="The customer ID")
    args = parser.parse_args()
    print(find_customer(args.id))
```

## Assignment

Look at the full eldrow code [here](https://github.com/vanatteveldt/eldrow/blob/main/eldrow.py). 

* Can you identify parts of the code that violate the DRY or separation of concerns?
* Which functions would you make to solve these issues?
* Do you think the code needs separate modules? 
* Refactor the code by making those functions/modules and changing the main logic. 
* Add `__main__`  code to separate command line usage from the created functions.
* Do you think the new code improves on the reusability and readability of the program? 

(Ideally, clone the code locally, start a new branch, and make your changes in the branch so we can compare them through a pull request! See our [previous workshop on using github](https://github.com/vanatteveldt/github-workshop))
