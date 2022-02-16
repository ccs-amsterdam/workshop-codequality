# Typing 
## 1. Why Type Hinting? 
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
useful and we will explore it in the following sections. 

## 2. Types in Different Languages 
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

## 3. Type Hinting in Python
To be aware of such possible bugs in our code in Python, we can use *type hinting*. 
It helps in avoiding such incompatibility errors when tested in the correct way. An
additional benefit is that it also adds more structure to your code and makes it 
clear for other users (including yourself) what is and can be expected from a function. 
It takes less time to understand what type of variable is expected as input and what 
the type of the output will be. 

We can indicate the _typing_ when defining a function or a class. Look at the example
below:
```python
def function_1(input_1: str, input_2: int) -> int: 
	return input_2
```
 This function simply returns the second input, which is expected to an integer. 
 We indicate this by adding _": TYPE"_ after the variable. We similarly define
 _input_1_ as a string. The type of the output is defined by adding an arrow and the 
 type after closing the function brackets: _func() -> TYPE_. 
 
There can be different types: int, string, list, dict etc. 

## 4. Different Use Cases
#### 4.1 Default Arguments
Sometimes, we require a default value for an input argument that is not always passed
to a function. We can set it up such cases in the following way: 
```python
def function_1(input_1: str, input_2: int = 0) -> int:
```
As we can see, the default value for _input_2_ is 0, and we apply the typing first, 
after which we assign the default value. 

#### 4.2 No Output
What if we have nothing to return in a function? Then we simply indicate that we are 
returning _None_: 
```python
def function_1(input_1: str, input_2:int) -> None: 
```

#### 4.3 Multiple Outputs
When returning multiple outputs, we indicate this with a _Tuple[type_1, ..., type_n]_: 
```python
def function_1(input_1: str, input_2:int) -> Tuple[str, int]: 
    return input_1, input_2
```

#### 4.4 More Than One Type
Lastly, when dealing with variables that could be more than one type, we can use the _Union_
type in the following way, where we indicate in the brackets which types can be expected: 
```python
def function_1(input_1: str, input_2:int) -> Union[str, int]: 
	if input_2 > 10: 
		return input_1
	return input_2

```
## 5. Testing
To test if the code can run correctly without any typing issues, we can make use of 
[mypy](https://mypy.readthedocs.io/). It is a type checker which will help in filtering
out bugs that are related to typing. 

#### 5.1 Installation
Installing can be simply done with the following 
command: 
```bash
$ pip install mypy
```

#### 5.2 Testing a File
Once the package is installed, we can test the types of a file in the following way: 
```bash
$ mypy FILE_NAME.py
```

#### 5.3 Testing a Directory
If we want to test for an entire directory, just replace the _FILE_NAME.py_ with the
name of the directory. 
```bash
$ mypy DIR_NAME
```

## 6. Further Reading
For further reading, we recommend the following links: 

1. [Type Checking Tutorial](https://realpython.com/python-type-checking/)
2. [Type Hinting Cheat Sheet](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html)
3. [mypy](https://mypy.readthedocs.io/en/stable/introduction.html)
