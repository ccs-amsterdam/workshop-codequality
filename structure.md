# Code structure

Consider the 'anti-pattern' wordle code in [eldrow.py](https://github.com/vanatteveldt/eldrow/blob/main/eldrow.py).
The program works, and the game is enjoyable, but the code is hard to read and maintain or reuse.
This is partly due to the **structure** of the code. 

In particular, the current code is almost entirely "imperative", meaning it contains commands that are executed in sequence.
For larger projects, this is often not optimal. 

In python (and equivalently in many other languages), the main ways to structure code is using *functions* and *classes*
This tutorial will not go into the basics of how to write a function or class. 
Rather, we will discuss when and in what way to use functions and classes to make code readable.
If you are uncertain about the syntax or workings of functions, classes, and modules, see this [brief explanation](syntax.md). 

(Note that I'm ignoring *modules* here to keep things simple, hopefully we will add a *packaging and dissemination* tutorial at some point)

There are two main best practices that are often quoted for sturcturing code: *Don't repeat yourself (DRY)*, *separation of concerns*,
and *You ain't gonna need it (YAGNI)*. To this, we add a third princple, namely *structure as documentation*. 

## DRY: Don't repeat yourself

The most important and obvious idea behind using functions is to avoid copy-pasting code, aka repeating yourself.
Quite often, you find yourself doing the same thing in multiple places in the code.

**What:** A very obvious example is copy-pasting code to repeat the same operation on multiple datasets, values, rows or columns. 
Rather than copy-pasting the operation, it is almost always better to turn it into some for of *for loop*. 
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

In this case, the direct benefit of turning the loop body into a fuction is not immediately clear.
However, this is very useful if operation is used in different places of the code. 
But also in a case like this, moving the code into a separate function can help for readability, 
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



## YAGNI: You ain't gonna need it

## Structure as documentation
