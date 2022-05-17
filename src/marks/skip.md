# Skipping Tests in `pytest`

There are times we want to skip tests. This may be because a test takes too long, or that a test is only relevant on a particular platform. In this blog post, we'll be looking at a few of the builtin core markers used for skipping tests.

- Link to pytest documentation: [Link](https://docs.pytest.org/en/7.1.x/)

## Links

- Code from this blog post: [Link](https://github.com/CoffeeBeforeArch/pytest/tree/main/src/marks/00_skip)
- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My Email: CoffeeBeforeArch@gmail.com

# Skipping Tests

Marks are a core part of `pytest`, and give us the ability to easily set metadata on test functions. In this section, we'll be looking at the two builtin `pytest` markers for skipping tests:

1. `@pytest.mark.skip` - For skipping tests unconditionally
2. `@pytest.mark.skipif` - For skipping tests conditionally

## Skipping Tests Unconditionally

Recall our simple test for a function that squares a number (`square`) from the [previous blog post](../intro/intro.md):

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

To unconditionally skip the test, we need only add the decorator `@pytest.mark.skip` above the test function:

```python
# A simple test for our function 'square'
@pytest.mark.skip
def test_square():
    num = 5
    result = square(num)
    assert result == num ** 2
```

When we execute the test using `pytest`, we see the following:

```
cba@cba$ pytest test_functions.py::test_square
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 1 item                                                                                    

test_functions.py s                                                                           [100%]

======================================== 1 skipped in 0.01s =========================================
```

We see our single test is collected, but is not executed. Instead, we see `1 skipped` in the test log.

Likewise, we can add the same marker to a test class (e.g., `TestClass` from the [previous blog post](../intro/intro.md)) to skip all tests in a particular class:

```python
# A simple test class
@pytest.mark.skip
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

When try and run these tests, we now get the following result:

```
cba@cba$ pytest test_classes.py::TestClass
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 2 items

test_classes.py ss                                                                            [100%]

======================================== 2 skipped in 0.01s =========================================
```

We see that both tests from the `TestClass` get collected, but both are skipped. If we wanted to skip just some of the tests in the class, we could add the `@pytest.mark.skip` decorator to the individual class methods.

Tests can also be skipped from within the body of a test. For example, we can add a call to `pytest.skip()` to the body of a test:

```python
# A simple test for our function 'square'
def test_square():
    pytest.skip()
    num = 5
    result = square(num)
    assert result == num ** 2

```

While skipping tests is useful, we often want to know why a test was skipped. To do this, we can add the the optional `reason` string to our mark/call to say why the test was skipped:

```python
@pytest.mark.skip(reason='Look! We are skipping this test!')
def test_square():
    num = 5
    result = square(num)
    assert result == num ** 2
```

When we now try and run this test, we see not only that the test is skipped, but our reason for why the test was skipped:

```
cba@cba$ pytest test_functions.py::test_square -rs
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 1 item

test_functions.py s                                                                           [100%]

====================================== short test summary info ======================================
SKIPPED [1] test_functions.py:17: Look! We are skipping this test!
```

Note, you may need to add `-rs` to enabled reporting (`r`) for skipped tests (`s`).

## Skipping Tests Conditionally

We sometimes want to skip test under certain conditions. For example, we may want to skip certain tests if they are run on a system with an earlier Python version than the test was intended to run with. For this, we can use the marker `@pytest.mark.skipif`.

For example, we can add a marker to our `test_square` test that says we should only run it if the Python version is `<= 3.6`:

```python
@pytest.mark.skipif(sys.version_info > (3, 6), reason='Test requires Python version <= 3.6!')
def test_square():
    num = 5
    result = square(num)
    assert result == num ** 2
```

Note, `skipif` requires the `reason` for skipping the test to be specified.

When we try and run our test with a later Python version (e.g., `3.9.7` on my machine), we see that our test is skipped:

```
cba@cba$ pytest test_functions.py::test_square -rs
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 1 item

test_functions.py s                                                                           [100%]

====================================== short test summary info ======================================
SKIPPED [1] test_functions.py:16: Test requires Python version <= 3.6!
======================================== 1 skipped in 0.01s =========================================
```

Note, we ran with `-rs` again to have the skip reason printed in the output log.

# Conclusion

Controlling which tests are run and under what what circumstances is a critical part of test development. In future blog posts, we'll be exploring some of the other builtin pytest markers that allow us to say that tests are expected to fail, and enable parametrization of tests.

Cheers,

--Nick

## Contact Information

- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My GitHub: [Link](https://github.com/CoffeeBeforeArch)
- My Email: CoffeeBeforeArch@gmail.com

