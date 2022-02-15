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

There are two main best practices that are often quoted for sturcturing code: *Don't repeat yourself (DRY)* and *separation of concerns*. 
To this, we add a third princple, namely *structure as documentation*. 

## Don't repeat yourself

## Separation of concerns: do one thing, and do it well

## Structure as documentation
