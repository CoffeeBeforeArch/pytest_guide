# Parametrizing Tests in `pytest`

We often want to test something like a function with a number of different inputs. With `pytest`, we can accomplish this through the built-in marker `@pytest.mark.parametrize`.

In this blog post, we'll be looking at the basics of test parametrization using `@pytest.mark.parametrize`

- Link to pytest documentation: [Link](https://docs.pytest.org/en/7.1.x/)

## Links

- Code from this blog post: [Link](https://github.com/CoffeeBeforeArch/pytest/tree/main/src/marks/02_parametrize)
- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My Email: CoffeeBeforeArch@gmail.com

# Test Parametrization

Recall our simple test for a function that squares a number (`square`) from the [previous blog post](xfail.md):

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

Let's say we want to test more than `num = 5` as the input to our function `square`. Instead of writing multiple tests for multiple `num` values, we vsn add the marker `@pytest.mark.parametrize` to our test.

We will pass two things to our marker:

1. A string with the name of our parameter (`num` in this case)
2. A list of values to call our `test_square` test with

Additionally, we will need to add the parameter name in our string to the parameter list of our test function. Here's what our new test should like if we parametrize our test with the input `num` over the values `1` to `5`:

```python
# A simple test for our function 'square'
@pytest.mark.parametrize('num', [1, 2, 3, 4, 5])
def test_square(num):
    result = square(num)
    assert result == num ** 2
```

If we run run with `--collectonly`, we see there are 5 variants of `test_square` (one for each of the values specified in the parametrize marker:

```
cba@cba$ pytest --collectonly test_functions.py 
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 6 items                                                                                   

<Module test_functions.py>
  <Function test_square[1]>
  <Function test_square[2]>
  <Function test_square[3]>
  <Function test_square[4]>
  <Function test_square[5]>

==================================== 6 tests collected in 0.02s =====================================
```

Each of these tests can be run individually (e.g., `pytest tests_functions.py::test_square[2]`). You can also run all the varaints for a single test by omitting the parameter list after the test name (i.e., the `[...]`):

```
cba@cba$ pytest test_functions.py::test_square
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 5 items                                                                                   

test_functions.py .....                                                                       [100%]

========================================= 5 passed in 0.01s =========================================
```

We see that all 5 of the tests are collected, run, and pass!

We often want to parametrize multiple values at the same time. For example, we may parametrize a test with both inputs and expected outputs.

To do this, we can pass to our marker a comma separated list of names in a string, and a list of tuples for the values (along with adding each named parameter to the parameter list of the test):

```python
# A simple test for our function 'square'
@pytest.mark.parametrize('num, ref', [(1, 1), (2, 4), (3, 9), (4, 16), (5, 25)])
def test_square(num, ref):
    result = square(num)
    assert result == ref
```

If we run collection for the test, we see there are now two parameters for each version of the function (separated by a `-`):

```
cba@cba$ pytest --collectonly test_functions.py
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 6 items

<Module test_functions.py>
  <Function test_square[1-1]>
  <Function test_square[2-4]>
  <Function test_square[3-9]>
  <Function test_square[4-16]>
  <Function test_square[5-25]>

==================================== 5 tests collected in 0.01s =====================================
```

You can also add multiple `@pytest.mark.parametrize` markers to a test. This is useful when parametrizing multiple inputs that are independent of each other.

For example, we can update our `test_square` to square two different numbers:

```python
# A simple test for our function 'square'
@pytest.mark.parametrize('num1', [1, 2, 3])
@pytest.mark.parametrize('num2', [4, 5, 6])
def test_square(num1, num2):
    # Test the first number
    result1 = square(num1)
    assert result1 == num1 ** 2
    
    # Test the second number
    result2 = square(num2)
    assert result2 == num2 ** 2
```

If we run collection for the test, we see 9 total tests are collected:

```
cba@cba$ pytest --collectonly test_functions.py
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 10 items

<Module test_functions.py>
  <Function test_square[4-1]>
  <Function test_square[4-2]>
  <Function test_square[4-3]>
  <Function test_square[5-1]>
  <Function test_square[5-2]>
  <Function test_square[5-3]>
  <Function test_square[6-1]>
  <Function test_square[6-2]>
  <Function test_square[6-3]>

==================================== 9 tests collected in 0.01s ====================================
```

When we have multiple `@pytest.mark.parametrize` markers, `pytest` will do a cartesian product of the parameters (i.e., it generate all possible combinations of parameters from the different parametrize markers).

It is also possible to add additional markers (e.g., `skip` or `xfail`) to specific values in a parametrization. This can be done with `pytest.param` and setting the optional `marks` argument.

For example, we can take our first parametrization of `test_square` and skip the value `3`:

```python
# A simple test for our function 'square'
@pytest.mark.parametrize('num', [1, 2, pytest.param(3, marks=pytest.mark.skip), 4, 5])
def test_square(num):
    result = square(num)
    assert result == num ** 2
```

Note, if you are setting multiple values with `@pytest.mark.parametrize` using tuples, you can separate the values with commas in `pytest.param`.

When we run all `test_square` variants, we see from the logs that all 5 tests are collected, but the test with input value `3` is skipped:

```
cba@cba $pytest test_functions.py::test_square
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 5 items

test_functions.py ..s..                                                                       [100%]

=================================== 4 passed, 1 skipped in 0.02s ====================================
```

# Conclusion

Parametrization is a core part of testing in order to get adequate test coverage. In this guide, we looked at how we can do easily parametrize tests with the `@pytest.mark.parametrize` marker. In future blog posts, we'll look at other useful features of `pytest` like test fixtures.

Cheers,

--Nick

## Contact Information

- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My GitHub: [Link](https://github.com/CoffeeBeforeArch)
- My Email: CoffeeBeforeArch@gmail.com

