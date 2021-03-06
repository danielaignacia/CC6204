# CC6204 Autocorrect

Basic tooling for autocorrecting programming homeworks for the CC6204: Deep Learning course at the University of Chile

## Instalation

Install either with
`pip install "git+https://github.com/dccuchile/CC6204.git@master#egg=cc6204&subdirectory=autocorrect"`
or with
`pip install "https://github.com/dccuchile/CC6204/archive/master.zip#subdirectory=autocorrect"`

## Usage
### Instantiate a corrector object:
```python
from cc6204 import AutoCorrect

corrector = AutoCorrect(host=<host>, port=<value>)
```

### Submit an answer for revision:
```python
corrector.sumbit(homework=1, question="1a", test=1, token=<token>, answer=...)
```
Submit will raise a `FailedTest` exception when the submited answer is not what we were expecting. If you want to know which values you submited were labeled incorrect, catch the exception and get the `mask` attribute as follows:
```python
from cc6204 import FailedTest

revision = None
try:
    corrector.sumbit(homework=1, question="1a", test=1, token=<token>, answer=...)
except FailedTest as e:
    print(e.comments)
    revision = e.mask

print(revision)
```

If you get all correct values, then no exception is raised and a `"Correct Test!"` string is printed to console.

### Get the tests being used for revision:
```python
test_input = corrector.get_test_data(homework=1, question="1a", test=1, token=<token>)
```
Use this `test_input` values as an input to you implementation and send the output as an answer to the `submit` method.

Some tests have arguments assigned to them, in that case the `get_test_data` method returns a tuple `(test_data, *arguments)` like so:
```python
test_input, param1, param2, ... = corrector.get_test_data(homework=10, question="1z", test=12, token=<token>)
```


### How to use it in your homework

First install the library and instantiate a corrector object with the `host`, `port` and `token` values given in U-cursos.

```python
from cc6204 import AutoCorrect, FailedTest

corrector = AutoCorrect(host=<host>, port=<value>)
```

Let's say you are working on Test 3, Question 2b in Homework 1. You would first implement the given question. Then ask the library for the test set to evaluate your implementation using the `get_test_data` method.
```python
def my_implementation(some_input):
    # my hard work
    ...
    return output

test_input = corrector.get_test_data(homework=1, question="2b", test=3, token=<token>)
```

Use the returned value as an input to your implementation and save the output value. Pass the output value to call the `submit` method and review your mistakes.
```python
my_answer = my_implementation(test_input)
revision = None
try:
    corrector.sumbit(homework=1, question="2b", test=3, token=<token>, answer=my_answer)
except FailedTest as e:
    print(e.comments)
    # this is a mask with the values you missed
    revision = e.mask

if revision is not None:
    # you made mistakes
    print(revision)
# if revision is None, then a "Correct Test!" printed in console and your implementation passed the test
```
