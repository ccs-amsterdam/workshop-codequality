# Unit Tests

## 1. What are Unit Tests?  
Often, after running our code and looking at the results we finally realize that there 
were some mistakes present which affect the final results. There is nothing else left 
but to re-run everything after fixing the mistake. This might take another pass as it 
becomes clear that there are other bugs present and in the end a lot of time is wasted 
on fixing the code. 

Here is where _unit tests_ come in. **Unit tests** are test cases that a piece of 
software has to pass. It examines if the code is running correctly and does not 
contain bugs that went undetected when coding. Unit tests are highly useful to 
prevent such a cycle of bug fixing and getting a lot of the code right in the 
first iteration already. 

This section will further talk about the utility of unit tests and how you can apply 
this method to your own project. 

## 2. Utility of Unit Tests
There are many cases for which unit tests can be useful. Think of the following cases 
for example: 
1. Messy code
2. Automatizing a check for several test cases 
3. Discover code-breaking changes

When it comes to messy code, the chance of overlooking a mistake increases tremendously.
While we encourage writing clean code from the get go, unit tests can help you catch big 
and/or tiny bugs that are easily missed. Think of accidental assignments of variables or
repetition of code that is fixed in one spot but left incorrect at another. 

Clean code can still suffer from bugs, for which unit tests can also help. Especially
when there are edge or corner cases that are left untested and fail at a later stage
when running the code. For instance, when a negative value is passed to a function that
returns the n-th (input value) number of the Fibonacci sequence. Hence, scaling up for 
several test cases can turn out to be beneficial as it is possible to cover many 
special and straightforward cases that should be solved. 

Furthermore, adding or modifying code is a normal part of improving and implementing 
new features. However, it is important to know that these changes do not affect the 
existing code. Imagine that introducing a new piece of code breaks a particular flow with
other code (like a _TypeError_ suddenly being introduced!), then unit tests can help in
discovering that a particular feature of the code does not work anymore and hence needs 
to be fixed. 

## 3. Approach to Creating Unit Tests
We have talked about modularity of the code before, in the previous sections, but the 
same approach is key for the creation of unit tests. When creating and thinking of 
unit tests, it is important to address the following questions: 
1. What do you want to achieve with your code? 
2. What are the essential things the code should be able to do? 
3. What are special cases we need to consider? 

Think of the end-goal and the sub-goals to reach it. It is hence very 
important to decompose your problems into sub-problems, so each different factor 
is covered by the unit tests. 

For instance, in the case of _wordle_, think about what the core features are? How can
those be addressed with unit tests? 

## 4. How to Create & Run Unit Tests
#### 4.1 Setup
Unit tests are created in a separate _tests_ folder. This tests folder follows the same
structure of the original project. For each file that you want to create unit tests 
for, you create a new file called _"test_ORIGINAL_FILENAME.py"_. 

To run unit tests, we can use [pytest](https://docs.pytest.org/en/7.0.x/), which we can
install: 
```bash
$ pip install pytest
```

More on its usage can be found in the following sections. 
#### 4.2 Simple Test Cases
In this file, we create the file-appropriate unit tests. Simple unit tests, where we 
test for one specific test case, we can use simple a `assert` to ensure that the 
functionality is working according to our own expectations. For instance, if we want 
to make sure that the _wordle_ functions return 5-letter words, we can `assert` that 
the length of a returned word is equal to 5: 
```python
def test_chosen_word(): 
    chosen_word = wordle.choose_word()
    assert len(chosen_word) == 5
```
More of such asserts can be added for such simple cases. For example, think of 
ensuring that the chosen word returned is indeed a string. 

#### 4.2 Automatizing Several Test Cases
But what if you want to test the same function for several different testcases? This is 
where `pytest` comes in. We can use the `pytest.mark.parametrize` functionality to 
ensure that several different test cases are tested for so many cases are covered. 
See the following example, where we want to test for different amounts of n that the 
same word is not returned more than once: 

```python
@pytest.mark.parametrize(
    "n",
    [
        1, 2, 3
    ],
)
def test_color_code(n):
    # Think of how you would setup this code! 
```
The first argument of the `parametrize` decorator is the order of the values for the
arguments that will be passed to the function. The second argument in the decorator is
a list of test cases that will be passed. In the example, these are three different 
cases. 

While we know the outcome for the different test cases in the example given above 
(outcome is not dependent on individual test cases), this might not always be the 
case. Each different test case could have a different expected outcome. In that case,
the expected outcome can also be given as an input argument and we can use `assert`
to check for the validity: 
````python
@pytest.mark.parametrize(
    "input1, expected_outcome",
    [
        (1, 1), 
        (2, 2), 
        (3, 3)
    ],
)
def test_func1(input1, expected_outcome):
    assert func1(input1) == expected_outcome
````

#### 4.3 Running the Unit Tests 
To run the unit tests and check how many of them pass, we can run the following command:
```bash
$ pytest tests/
```
This will run all of the unit tests in the tests folder and will illustrate which ones
went wrong. It will show the expected output and what the actual output was. 

## 5. Unit Tests and GitHub Actions
To prevent forgetting to run these tests, we can make use of GitHub Actions. When 
pushing a new commit to the repo, GitHub Actions automatically runs the unit tests
for you (and any other type of tests you would like to run). It makes it clear which
commits are working and which ones introduce code-breaking changes. 
More information on this can be found [here](https://github.com/features/actions).

## 6. Future Reading
For further reading on this topic, we would like to refer to the following links: 
1. [Python Unit Testing](https://realpython.com/python-testing/)
2. [Pytest](https://docs.pytest.org/en/7.0.x/getting-started.html)
3. [Quickstart for GitHub Actions](https://docs.github.com/en/actions/quickstart)