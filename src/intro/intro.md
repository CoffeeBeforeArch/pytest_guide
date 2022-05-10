# PyTest Introduction

Testing is a critical part of developing large and complex software. In this first guide, we'll be looking at the basics of writing tests using the PyTest framework.

Specifically, we'll be looking at how to write test as individual functions, and grouping tests into a single class.

## Links

- Code from this blog post: [Link](https://github.com/CoffeeBeforeArch/pytest/tree/main/src/intro/00_intro)
- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My Email: CoffeeBeforeArch@gmail.com

# Writing Tests

In this section, we'll cover the basics of writing tests with `pytest`. This includes writing stand-alone tests as functions, and grouping tests into classes.

What exactly are we going to be testing? Let's test the two simple functions, `square` and `cube`:

```python
# A simple function that squares a number
def square(num):
    return num * num


# Simple function that cubes a number
def cube(num):
    return square(num) * num
```

`square` simply squares a number by multiplying an input number `num` by itself. Similarly, `cube` cubes a number by multiplying an input number `num` by the result of `square`. These aren't terribly interesting, but they will serve just fine as functions we can test.

## Test Files

By default, `pytest` will look for tests in files starting with `test_`. For example, if we have a file named `test_functions.py` or `test_classes.py`, `pytest` will look for valid tests within those files.

## Test Functions

Functions are one way we can specify tests with `pytest`. Similar to how `pytest` looks for tests in files starting with `test_`, `pytest` will also (by default) only consider functions starting with `test_` as tests.

For example, we can write a simple test for a function, `square`:

```python
# A simple test for our function 'square'
def test_square():
    num = 5
    result = square(num)
    assert result == num ** 2
```

In this example, `pytest` will find a single test (`test_square`) in our file. The function `square` will not be considered a test because it doesn't start with `test_`.

Multiple tests can live in the same file. For example, we could add another test for our function `cube` in the same file as `test_square`:

```python
# A second test in our file for the function 'cube'
def test_cube():
    num = 5
    result = cube(num)
    assert result == num ** 3
```

We'll keep both of these test functions (and the functions we're testing) in a file called `test_functions.py`.

## Test Classes

We often want to group related tests so they can share things like common methods. We can do just this by writing tests as methods of a class.

Just like how `pytest` searches for tests in files and functions with specific names, the same is true for classes. `pytest` will only look for tests in classes starting with `Test`, and class methods starting with `test_`.

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

We'll keep our `TestClass` with our test methods (and the functions we're testing) in a file called `test_classes.py`.

# Running Tests

Now that we have some background on what tests look like, we'll learn how to both query available tests and run them. For this, we'll be using our `test_functions.py` and `test_classes.py` files we previously created.

## Finding Available Tests with `collectonly`/`collect-only`

Before tests are executed, `pytest` goes through a phase called `collection`. During this phase, `pytest` will look for tests that it can execute.

We often want to see what tests are available before we execute them (often because we want to run specific tests, not every available test). We can do this by running `pytest --collectonly`/`pytest-collect-only` followed by the path to the directory containing tests (if no path is specified, the current working directory is searched).

For example, if we run `pytest --collectonly` in the directory with our `test_functions.py` and `test_classes.py` files, we can see the following:

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

Additionally, you can point `pytest` to a single file to see the tests available in that file. For example, here's the collect only log for our `test_functions.py` file:

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

Now, only two tests are collected (our test functions `test_square` and `test_cube` from `test_functions.py`).

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

To run tests in a particular file, we need only pass the full path to the file. For example, here is the results of running all tests in our `test_functions.py` file:

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

We can see that there is a hierarchy for our tests (`Module`, `Class` in the case of `TestClass`, and `Function`). We can narrow the execution of our tests by specifying levels of this hierarchy in our `pytest` command.

As we've already seen, we run only tests within a `Module` (e.g., our file `test_functions.py`) by specifying a file name in our `pytest` command. To add more levels of the test hierarchy to our `pytest` command, we can join them together separated by `::`. For example, we can run just `test_square` from our `test_functions.py` folder using `pytest test_functions.py::test_square` (specifying both the `Module` and `Function`):

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

Now, only our 2 tests part of `TestClass` are collected and run (and pass!).

# Conclusion

By now, you should have a basic understanding of how tests written in `pytest` look. In the remainder of these guides, we'll be looking at a number of the builtin `pytest` features that make writing tests easier.

Cheers,

--Nick

## Contact Information

- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My GitHub: [Link](https://github.com/CoffeeBeforeArch)
- My Email: CoffeeBeforeArch@gmail.com

