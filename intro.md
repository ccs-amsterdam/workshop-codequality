# Writing better code: from script to software

*Beautiful is better than ugly.*<br/>
*Explicit is better than implicit.*<br/>
*Simple is better than complex.*<br/>
*Complex is better than complicated.*<br/>
*Flat is better than nested.*<br/>
*Sparse is better than dense.*<br/>
*Readability counts.*<br/>
*Special cases aren't special enough to break the rules.*<br/>
*Although practicality beats purity.*<br/>
*Errors should never pass silently.*<br/>
*Unless explicitly silenced.*<br/>
– `import this`: from *the Zen of Python* by Tim Peters

Ideally, code should be readable, so that when someone else reads your code, they immediately understand what you’re doing and why. 
Also, code should be reusable, in the sense that others can easily extend or adapt your code to their problem. 
Generally, like a well-written paper, this implies that code should be as simple and explicit as possible, 
without unneeded complications or repetitions, and with every part of the code serving a clear purpose, 
just like every paragraph and section in a paper has its purpose.

Now, remember that the most likely “other person” to be staring at your code is your future self. 
A year from now, when you finally get the R&R for your paper or your are starting a follow-up for your study, 
you probably won’t remember much about why you made certain coding decisions or how you used the code. 
Thus, by making it readable and reusable you are helping yourself as much as the community. 

If you make it a habit to write code neatly to start with, 
and crucially to spend a brief time after finishing the study or submitting the paper to refactor and tidy up the code for publication, 
you will save a lot of time and frustration in the long term. 
Moreover, since for a computational social scientist your github profile is part of your CV, 
clean and well-documented code is an external mark of competence. 

In this workshop, we will try to help you achieve this by considering four aspects that are crucial to clean code, 
but not often taught in a computational social science curriculum: [Structure](structure.md), [Documentation](documentation.md),
[Typing](typing.md), and [Testing](testing.md). 

As beauty is in the eye of the beholder, in many cases what is clean partly depends on personal style. What one person considers brief or compact the other considers terse or hard to read. However, on each of those aspects we can offer some general best practices, considerations, and tips from many years in the business of writing software. 

As a guiding example, we will use a “anti-pattern” (i.e. not very clean) implementation of wordle as a terminal game: https://github.com/vanatteveldt/eldrow. As part of the explanations in each section we will look at and refactor aspects of that code. As a challenge, we ask you to create a cleaner version of the program and we can compare the various ‘solutions’ at the end of the workshop. 

Note that although this workshop is taught with python as a medium of instruction, in general terms most principles are applicable regardless of language
(presumably with the exception of type hinting).

