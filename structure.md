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

There are three common best practices that are often quoted for sturcturing code: *Don't repeat yourself (DRY)*, *separation of concerns*,
and *You ain't gonna need it (YAGNI)*. 

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

COLORS = {"CORRECT": Back.GREEN, "INWORD": Back.YELLOW, "ONCORRECT": ""}
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
