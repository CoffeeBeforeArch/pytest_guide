# PyTest Introduction

Testing is a critical part of developer large and complex software. In this first guide, we'll be looking at the basics of writing tests using the PyTest framework.

Specifically, we'll be looking at how to write test as individual functions, and grouping tests into a single class.

# Writing Tests

## Test Files

By default, `pytest` will look for tests in files starting with `test_`. For example, we have a file named `test_functions.py` or `test_classes.py`, and `pytest` will look for valid tests within the file.

## Test Functions

Functions are one way we can specify tests with `pytest`. Similar to how `pytest` looks for tests in files starting with `test_`, `pytest` will also (by default) only consider functions starting with `test_` as tests.

For example, we can write a simple test for a function, `square`, that multiples a number by itself:

```python
# A simple function that squares a number
def square(num):
    return num * num

# A simple test for our function 'square'
def test_square():
    num = 5
    result = square(num)
    assert result == num ** 2
```

In this example, `pytest` will find a single test (`test_square`) in our file (`square` will not be considered a test because it doesn't start with `test_`).

Multiple tests can also live in the same file. For example, we could add another test for a function called `cube` in the same file as `test_square`:

```python
# Simple function that cubes a number
def cube(num):
    return square(num) * num

# A second test in our file for the function 'cube'
def test_cube():
    num = 5
    result = cube(num)
    assert result == num ** 3
```

## Test Classes

We often want to group related tests together so that they can share things like common methods. We can do just this writing tests as methods of a class.

Similar to how `pytest` will look search for tests in files and functions with specific names, the same is true for classes. `pytest` will only look for tests in classes starting with `Test`, and class methods starting with `test_`.

For example, we can group both of our tests from our previous example (`test_square` and `test_cube`) into a single class called `TestClasses`:

```python
# A simple test class
class TestClass:
    # Multiple tests can now use the same number
    num = 5

    # Test our function 'square'
    def test_square(self):
        result = square(self.num)
        assert result == self.num ** 2

    # Test our function `cube`
    def test_cube(self):
        result = cube(self.num)
        assert result == self.num ** 3
```

One important thing to note is that each of our tests will get their own copy for the `TestClass` object (they will not share the same object)

# Running Tests

Now that we have some background on what tests look like, we can look at how to find available tests and how to run them.

## Seeing Available Tests with `collectonly`/`collect-only`

Before tests are executed, `pytest` goes through a startup phase called `collection`. During this phase, `pytest` will see what tests are available for execution.

We often want to see what tests are available before we execute them (often because we want to run specific tests, every available test). We can do this by running `pytest --collectonly`/`pytest-collect-only` followed by the path to the directory containing tests (if no path is specified, the current working directory is searched).

For example, if we run `pytest --collectonly` in the directory with our test functions/class, we can see the following:

```
cba@cba$ pytest --collect-only
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 4 items

<Module test_classes.py>
  <Class TestClass>
    <Function test_square>
    <Function test_cube>
<Module test_functions.py>
  <Function test_square>
  <Function test_cube>

==================================== 4 tests collected in 0.01s =====================================
```

From this, we can see that all four of our tests have been collected (`test_square` and `test_cube` that we defined as functions, and the methods of the same name that are part of `TestClass`).

Additionally, you can point pytest to a single file to see the tests available in that file. For example, here's the collect only log for our `test_functions.py` file:

```
cba@cba$ pytest --collectonly test_functions.py
================================ test session starts =================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 2 items

<Module test_functions.py>
  <Function test_square>
  <Function test_cube>

============================= 2 tests collected in 0.01s =============================
```

Now, only two tests are collected (our test functions `test_square` and `test_cube`).

## Running Tests

We can run all tests in a directory by invoking `pytest` with the path to the directory with our tests (by default, it will run all tests in your current working directory).

For example, here is the result of running all of the tests defined in `test_functions.py` and `test_classes.py`:

```
cba@cba$ pytest
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 4 items

test_classes.py ..                                                                            [ 50%]
test_functions.py ..                                                                          [100%]

========================================= 4 passed in 0.02s =========================================
```

We can see from the logs that all 4 of our tests were collected, and all 4 passed!

To run tests from a particular file, we need only pass the full path to the file. For example, here is the results of running all the tests in our `test_functions.py` file:

```
cba@cba$ pytest test_functions.py
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 2 items

test_functions.py ..                                                                          [100%]

========================================= 2 passed in 0.01s =========================================
```

Now, only our 2 tests in `test_functions.py` are collected and run (and pass!).

We can also run a specific test from within a file. Recall our previous results from running `--collect-only`:

```
cba@cba$ pytest --collect-only
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 4 items

<Module test_classes.py>
  <Class TestClass>
    <Function test_square>
    <Function test_cube>
<Module test_functions.py>
  <Function test_square>
  <Function test_cube>

==================================== 4 tests collected in 0.01s =====================================

```

We can see that there is a hierarchy for our tests (`Module`, `Class` in the case of `TestClass`, and `Function`, which is our test function). We can narrow the execution of our tests by specifying more levels of this hierarchy in our `pytest` command.

As we've already seen, we run only tests within a `Module` (e.g., our file `test_functions.py`) by specfiying the file name in our `pytest` command. We can add more levels of the test hierarchy to our `pytest` command, separated by `::`. For example, we can run just `test_square` from our `test_functions.py` folder using `pytest test_functions.py::test_square`:

```
cba@cba$ test_functions.py::test_square
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 1 item                                                                                    

test_functions.py .                                                                           [100%]

========================================= 1 passed in 0.01s =========================================
```

Here, only a single test (`test_square`) is collected and run by `pytest` (and passes!). Likewise, we could run just tests from a specific class (`TestClass` in our case):

```
cba@cba$ pytest test_classes.py::TestClass
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 2 items

test_classes.py ..                                                                            [100%]

========================================= 2 passed in 0.01s =========================================
```

Now, only our two tests part of `TestClass` are collected and run (and pass!).

# Conclusion

By now, you should have a basic understanding of how tests written in `pytest` look. In the remainder of these guides, we'll be looking at a number of the builtin `pytest` features that make writing tests easier.

