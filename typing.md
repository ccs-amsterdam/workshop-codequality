# Typing 
## Why Type Hinting? 
Due to the messiness of the initial wordle code, _TypeErrors_ could easily occur when
running the code. These errors occur when there are operations in the code that are being
applied to types that are incompatible with each other. Think of when you are trying
to slice an integer: 

```python
>>> var1 = 9
>>> var1[0]
TypeError: 'int' object is not subscriptable
```

Or doing a summation with an integer and a string: 
```python
>>> var1 = 9
>>> var2 = "hello world"
>>> var1 + var2
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

While these simple examples are easily spotted, in a complex set of code this will not
be the case. As we pass variables around different functions, there is a higher chance 
of such potential errors going undetected. Hence, using type hinting in Python can be highly
useful and you will get familiar with it in the following sections. 

## Types in Different Languages 
While other languages, e.g. Java have static typing: when initializing a variable, 
the type (Int, String, etc) has to be declared, Python does not make use of such 
declarations. See below two examples of how both handle the 
assignment of a different type to the same variable. 

```javascript
Java
----
String word = "hello";
// The following will throw an error: 
word = 9;
```

```python
Python
------
word = "hello"
# This is acceptable.
word = 9
```
This highlights how unexpected type changes can easily go unnoticed in Python. Hence, 
it the usage of **type hinting** can be beneficial. 

## Type Hinting in Python
To be aware of such possible bugs in our code in Python, we can use **type hinting**. 
It helps in avoiding such incompatibility errors when tested in the correct way. An
additional benefit is that it also adds more structure to your code and makes it 
clear for other users (including yourself) what is and can be expected from a function. 
It takes less time to understand what type of variable is expected as input and what 
the type of the output will be. 

We can indicate the _typing_ when defining a function or a class. Look at the example
below:
```python
def evaluate_character(word: str, guess: str, index: int) -> str:
    if guess[index] == word[index]:
        return "CORRECT"
    if guess[index] in word:
        return "INWORD"
    return "INCORRECT"
```
 This function expects its input arguments to be strings and an integer. 
 We indicate this by adding _": TYPE"_ after the variable. The type of the output is defined by adding an arrow and the 
 type after closing the function brackets: _func() -> TYPE_. In this case, the output 
 is a string. Now this can avoid _TypeErrors_ such as indexing `guess` with a string or 
 indexing an integer. 
 
There can be different types: int, string, list, dict etc. In the links for further 
reading, an overview of other types can be found. 

## Different Use Cases
#### Default Arguments
Sometimes, we require a default value for an input argument that is not always passed
to a function. We can set it up such cases in the following way: 
```python
def evaluate_character(output: List[str], word: str, guess: str, index: int, reset: Bool = True) -> List[str]:
```
As we can see, the default value for _reset_ is True, and we apply the typing first, 
after which we assign the default value. 

#### No Output
What if we have nothing to return in a function? Then we simply indicate that we are 
returning _None_: 
```python
def evaluate_character(output: List[str], word: str, guess: str, index: int) -> None:
```

#### Multiple Outputs
When returning multiple outputs, we indicate this with a _Tuple[type_1, ..., type_n]_: 
```python
def function_1(input_1: str, input_2:int) -> Tuple[str, int]: 
    return input_1, input_2
```

#### More Than One Type
Lastly, when dealing with variables that could be more than one type, we can use the _Union_
type in the following way, where we indicate in the brackets which types can be expected: 
```python
def function_1(input_1: str, input_2: int) -> Union[str, int]: 
	if input_2 > 10: 
		return input_1
	return input_2

```
## Testing
To test if the code can run correctly without any _TypeErrors_, we can make use of 
[mypy](https://mypy.readthedocs.io/). It is a type checker which will help in filtering
out bugs that are related to typing. Some IDEs (e.g. PyCharm) also highlight such incompatibilities.  

#### Installation
Installing can be simply done with the following 
command: 
```bash
$ pip install mypy
```

#### Testing a File
Once the package is installed, we can test the types of a file in the following way: 
```bash
$ mypy FILE_NAME.py
```
The output will indicate on which lines the errors can be found. 
#### Testing a Directory
If we want to test for an entire directory, just replace the _FILE_NAME.py_ with the
name of the directory. 
```bash
$ mypy DIR_NAME
```
Per file in the directory, the errors will be indicated. 

## Assignment
1. Take a look at your improved _wordle_ code again and add type hints to the functions
you just created! 
2. Could you get rid of any _TypeErrors_? 
 
## Further Reading
For further reading, we recommend the following links: 

1. [Type Checking Tutorial](https://realpython.com/python-type-checking/)
2. [Type Hinting Cheat Sheet](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html)
3. [mypy](https://mypy.readthedocs.io/en/stable/introduction.html)
