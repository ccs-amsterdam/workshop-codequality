# Documentation

*Explicit is better than implicit [..] readability counts.*

When you are reading someone else's source code (or the script you wrote yourself last year),
you are often struggling to understand exactly what, when, how, and why the code is doing. 
Although ideally the code itself will make as much as possible of this clear and explicit without needing additional documentation,
in practice this is often not the case. 

Specifically, code often shows *what* is happening, but not *why* it is happening or *how* it should be used.
For this, we mostly turn to inline *comments* and to *docstrings* per function. 

## How and when to use inline comments

**What:** Probably the most well known way of documenting code is the `inline comment`, in python any part of a line after a hashtag. 
This can be used to add explanation to the current or proceeding line(s) of code. 

For example, when we call `init()` to initialize the `colorama` package used, we could add a comment explaining that call:

```{python}
from colorama import init

# ... some lines of code ...

# initialize colorama
init()
```

And surely, that is helpful information to have, as just `init()` doesn't give a lot of information. 
However, it turns out that in many cases comments should be used sparingly.
Specifically, our advice is **comment the why, not the what** and **don't use comments when you can rewrite code instead**. 

### Comment the why, not the what

Suppose we see the following code:

```
# increment d by 1
d = d + 1
```

The comment on the first line is not really useful: everybody who speaks python can see that d is being incremented. 
Even worse, suppose that the code is changed later, but the comment wasn't updated, the reader is now left in even greater confusion 
about the actual and intended working of the program:

```
# increment d by 1
d = d - 1   # ???
```

The reason for this is that the comment is redundant with the code, so you are repeating yourself by saying the same thing twice,
which actually decreases readability and is error prone. 
Instead of commenting on what the code is doing, you should comment on why the code is doing that:

```
# The input includes the section header, so increment d to take this into account
d = d + 1
```

This comment is actually helpful to understand why `d` needs to be incremented, and can help a lot later if e.g. the input data format is changed. 

### Don't use comments if you could rewrite the code to make it clear by itself

Of course, sometimes code is not trivial to understand, and it can be really helpful to explain what code is trying to do.
Suppohttps://github.com/vanatteveldt/eldrow/blob/main/eldrow.pyse we have the following line:

```{python}
x = [w.title.replace("<br/>","") for w in widgets if w.price * inv.purchases(w.id) > avg_turnover]
```

Surely it would take a lot of stress away from your reader (or future you) by placing a small explanatory note there, right?

```{python}
# list the item titles with turnover higher than average
x = [w.title.replace("<b>","") for w in widgets if w.price * inv.purchases(w.id) > x]
```

And yes, it would probably be better to have that comment, than to not have that comment. 
However, the comment is still only trying to say what the line does, not how it does it. 

With a small nod to Wittgenstein, our advice would be: 
whenever a block of code is complex enough to need explanation,
try rewriting it into simpler code that doesn't need the explanation.  
For example, for that line of code this could be something like:

```{python}
def turnover(widget, inventory):
  return widget.price * inventory.purchases(widget.id)

def clean_title(widget):
  return widget.title.replace("<br/>","")

selected_titles = [clean_title(w) for w in widgets if turnover(w) > avg_turnover]
```

Now, the last line is quite readable: we are looking for a list of cleaned titles or widgets whose turnover is higher than average.
Although this is more lines of code in total, there's a good chance that the functions will be reused in other places, e.g. in calculating the average turnover.

Similarly, in the example of initializing colorama discussed earlier, you could also rewrite the import to avoid the need for a comment:

```{python}
import colorama

# ... some lines of code ...
colorama.init()
```

### Structure as documentation

One point that we specifically want to make is that good structure is often the most important documentation. 
By splitting blocks of code into functions with one clear purpose, and giving the function itself and the parameter useful names,
you make the logic behind the code *explicit* in the structure of the code itself. 

Consider again the original code snippet from the [eldrow](https://github.com/vanatteveldt/eldrow/blob/main/eldrow.py) anti-example:

```{python}
if guess[0] == word[0]:
    output.append(Back.GREEN)
elif guess[0] in word:
    output.append(Back.YELLOW)
output.append(guess[0])
output.append(Style.RESET_ALL)
```

To understand this code, you need to already understand how wordle uses colors, and make the link between the comparisons and the output codes. 

Of course, you could add comments here to describe this:

```{python}
# If the guessed letter is the same as that letter in the word, color it green:
if guess[index] == word[index]:
    output.append(Back.GREEN)
# Otherwise, if the letter does occur in the word color it yellow:
elif guess[index] in word:
    output.append(Back.YELLOW)
# Output the letter itself and reset the color:
output.append(guess[index])
output.append(Style.RESET_ALL)
```

Of course, this is fine and probably better than not having the comments. However, if you look at the refactored version of this code from the module on code structure, you will find that it is already much more readable even without needing any comments:

```{python}
for i in range(len(guess)):
    evaluation = evaluate_character(word, guess, i)
    coded_char = color_code_character(guess[i], evaluation)
    output.append(coded_char)
```

## The use of docstrings

The inline comments or code changes discussed above are all aimed at people directly reading the code in order to understand or adapt it. 
However, a main use case of writing clean and re-usable code is to tell other people how to use your code. 
In many cases, that would mean importing your code and calling one or more functions. 


**What:** To be able to do this, a user doesn't only need to know what the name and arguments of a function are,
but also understand what the function is supposed to do and the meaning/semantics of the arguments and return value. 

**How:** To add this information, we can use 'docstrings' (documentation strings). A docstring in Python is a string literal placed immediately after the function header:

```{python}
def turnover(widget, inventory): 
    """
    Compute the total turnover for this widget.
    """
    return widget.price * inventory.purchases(widget.id)
```

By convention, the docstring is made using a triple-quoted string, either a short one-liner or spread over multiple lines. 
Also by convention, the docstring typically consists of:

+ A one-line summary of what the function does (phrased as a command and ending with a period)
+ If needed, a longer explanation of the function
+ The meaning/semantics of the accepted arguments (see also the [next section on type hinting](typing.md)
+ The possible return value(s) and semantics

(ee also [PEP-0257](https://www.python.org/dev/peps/pep-0257/), the 'official' style guide for docstrings)

**When:** In short, almost every function should be given a docstring, even internal functions. In many cases, a simple one-liner is fine,
but for functions you expect other people to use it's best to be more verbose and cover various use cases and edge cases
(for example, you can specify how the function will react to erronous input - will it return None, raise an exception, or do something else?)

What you should *not* do is repeat the signature from the function header. Thus something like the docstring below is not useful, as it doesn't actually give any new information:

```{python}
def get_turnover(widget, inventory): 
   """Get the turnover from widget and inventory."""
```

### How are docstrings used?

Docstrings can be accessed in many places where you use a function.
For example, most IDEs like pycharm will display the docstring if you hover over a function call.
When using python interactively, you can use help(function) to display the docstring on the screen. 
Finally, if you publish your software or tool on e.g. github,
you can automatically generate documentation from the docstrings in your code.

For most python scripts, `pdoc` is probably the easiest system to set up and use. 
To test it out, create one or more functions with docstrings in your script, and run

```
pip install pdoc
pdoc scriptname.py 
```

This should generate the documentation and open a browser to view it locally. 
You can also save the generated html, e.g. to a docs folder:

```
pdoc scriptname.py -o docs
```

And from there it's super easy to setup a github site to publish it.

Alternatively, you can use the competing `pdoc3` to directly generate markdown files and link them from your `README.md`

For a real world example in a slightly different use case, see https://github.com/ccs-amsterdam/amcat4/blob/master/apidoc.md which 
uses `flask-autodoc` to generate API documentation from the docstrings on the flask routes such as from https://github.com/ccs-amsterdam/amcat4/blob/master/amcat4/api/index.py#L33. 


