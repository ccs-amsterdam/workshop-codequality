# Documentation

*Explicit is better than implicit, and readability counts.*

When you are reading someone else's source code (or the script you wrote yourself last year),
you are often struggling to understand exactly what, when, how, and why the code is doing. 
Although ideally the code itself will make as much as possible of this explicit

## Structure as documentation

**What:** We will discuss documentation in more detail in the next module, and see how functions play a central role in writing structured documentation.
However, even the example above makes it clear how functions (and structure in general) already provice a form of documentation.
By splitting blocks of code into functions with one clear purpose, and giving the function itself and the parameter useful names,
you make the logic behind the code *explicit* in the structure of the code itself. 

**How:** 
Consider again the original code snippet:

```{python}
if guess[index] == word[index]:
    output.append(Back.GREEN)
elif guess[index] in word:
    output.append(Back.YELLOW)
output.append(guess[index])
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

Of course, this is fine and probably better than not having the comments. However, 

```{python}
for i in range(len(guess)):
    evaluation = evaluate_character(word, guess, i)
    coded_char = color_code_character(guess[i], evaluation)
    output.append(coded_char)
```
