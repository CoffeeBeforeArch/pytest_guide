# Introduction to `pytest` Fixtures

Tests often require some initial setup, and it is often the case that different tests require the same setup. To handle the initialization of our test functions, we can use fixtures.

In this blog post, we'll be looking at the basics of `pytest` fixtures, how to write them, and some of the ways in which they can be configured.

- Link to pytest documentation: [Link](https://docs.pytest.org/en/7.1.x/)

## Links

- Code from this blog post: [Link](https://github.com/CoffeeBeforeArch/pytest/tree/main/src/fixtures/00_basic_fixtures)
- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My Email: CoffeeBeforeArch@gmail.com

# `pytest` Fixtures

In the [previous blog post](../marks/parametrize.md), we continued looking at our simple test, `test_square`:

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

In most of our examples, we've been hard-coding the input to our `square` function as `num = 5`. Instead, we can write a fixture that will provide `num` for us. This helps us separate our test setup code from the test body:

```python
@pytest.fixture
def num():
    return 5
```

Our test can request our fixture by adding the name of the fixture to its parameter list:

```python
# A simple test for our function 'square'
def test_square(num):
    result = square(num)
    assert result == num ** 2
```

When we run the test, our fixture will provide the value of `5` for `num`. Note, unlike with `@pytest.mark.parametrize` marker, test fixtures will not show up as parameters to the test when we run collection:

```
cba@cba$ pytest test_fixtures.py --collectonly
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/fixtures/00_basics_fixtures
plugins: benchmark-3.2.2
collected 1 item                                                                                    

<Module test_fixtures.py>
  <Function test_square>

===================================== 1 test collected in 0.00s =====================================
```

Fixtures can be used by multiple tests (i.e., two or more tests can request the same fixtures). For example, both our `test_square` and `test_cube` tests can request our `num` fixture:

```python
# A simple test for our function 'square'
def test_square(num):
    result = square(num)
    assert result == num ** 2

# A simple test for our function 'cube'
def test_cube(num):
    result = square(num)
    assert result == num ** 3
```

Tests can also request multiple fixtures. For example, we could have two fixtures (e.g., `num1` and `num2`) requested by `test_square`:

```python
@pytest.fixture
def num1():
    return 5

@pytest.fixture
def num2():
    return 10

# A simple test for our function 'square'
def test_square(num1, num2):
    result1 = square(num1)
    assert result1 == num ** 2
    
    result2 = square(num2)
    assert result2 == num ** 2
```

A common in testing is that we have some kind of common setup needed by all our tests. One solution to this problem is including a fixture that performs the setup in every test. However, this is prone to errors as test authors can easily forget to include the fixture (especially if there are many fixtures/parameters in the parameter list).

We can avoid this problem in `pytest` by specifying that a fixture should always be used, even if it is not specified in the parameter list of a test. This is done by setting `autouse=True` in the `@pytest.fixture` decorator.

For example, we can add another fixture, `log_start`, to our test that notifies us that a test is starting:

```python
@pytest.fixture
def num():
    return 5

@pytest.fixture(autouse=True)
def log_start():
    print('Test Starting!')

# A simple test for our function 'square'
def test_square(num):
    result = square(num)
    assert result == num ** 2
```

When we run the test with `-s` to disable output capturing (so we can see the print), we get the following:

```
cba@cba$ pytest test_fixtures.py::test_square -s
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/fixtures/00_basics_fixtures
plugins: benchmark-3.2.2
collected 1 item

test_fixtures.py Test Starting!
.

========================================= 1 passed in 0.00s =========================================
```

We see our string `Test Starting!` is printed from our fixture even though we did not specifically request the fixture. Note, you are allowed to directly request `autouse` fixtures.

Another useful feature of fixtures is shared state. We often have a fixture that provides the same value(s) to multiple tests. The data our fixture provides may be expensive to compute, so ideally we would like to re-use the results. We can do just this by setting the `scope` in the fixture decorator.

`scope` tells `pytest` when it should invoke a fixture. The valid scopes are:

- `function` (the default)
- `class`
- `module`
- `session`

For example, the default scope of `function` tells `pytest` that the fixture should be invoked once per function.

Consider our simple tests from earlier (`test_square` and `test_cube`) that use the same fixture (`num`). We can change the `scope` of the fixture `num` to `module` so that the fixture is only invoked once, and the result is re-used for other tests (in the module). We will also add a print to the fixture to help illustrate this point:

```python
@pytest.fixture(scope='module')
def num():
    print('Generating an input value!')
    return 5

# A simple test for our function 'square'
def test_square(num):
    result = square(num)
    assert result == num ** 2

# A simple test for our function 'cube'
def test_cube(num):
    result = square(num)
    assert result == num ** 3
```

When we run run both tests in our module, we get the following:

```
cba@cba pytest test_fixtures.py -s
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/fixtures/00_basics_fixtures
plugins: benchmark-3.2.2
collected 2 items                                                                                   

test_fixtures.py Generating an input value!
..

========================================= 2 passed in 0.00s =========================================
```

We see that both tests are collected and run (and pass), but the print from our fixture is only executed once!

If we switch the scope of our fixture back the default `function`, we get the following:

```
cba@cba$ pytest test_fixtures.py -s
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/fixtures/00_basics_fixtures
plugins: benchmark-3.2.2
collected 2 items

test_fixtures.py Generating an input value!
.Generating an input value!
.

========================================= 2 passed in 0.01s =========================================
```

Two prints from our fixture! It was invoked twice (once per test in our module)!

# Conclusion

Fixtures provide test writers the ability to move setup code out of test functions/classes in a way that makes it scalable/re-usable. They also provides a number of useful builtin features like result caching for a specified scope and automatic use.

In future posts, we'll be looking at some of the more advanced usage of `pytest` fixtures.

Cheers,

--Nick

## Contact Information

- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My GitHub: [Link](https://github.com/CoffeeBeforeArch)
- My Email: CoffeeBeforeArch@gmail.com

