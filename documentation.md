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
Suppose we have the following line:

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

selected_titles = [clean_title(w) for w in widgets if turnover(w) > avg_turnover
```

Now, the last line is quite readable: we are looking for a list of cleaned titles or widgets whose turnover is higher than average.
Although this is more lines of code in total, there's a good chance that the functions will be reused in other places, e.g. in calculating the average turnover.

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
However, a main use case of writing clean and re-usable code [..]
