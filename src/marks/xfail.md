# Expecting Test Failures in `pytest`

There are times that we expect our tests to fail. This may be because we have written our tests before the implementation of some feature is complete, or because we are specifically testing a failure path in our code.

In this blog post, we'll be looking at how to use the `pytest.mark.xfail` marker to mark tests that we expect to fail.

- Link to pytest documentation: [Link](https://docs.pytest.org/en/7.1.x/)

## Links

- Code from this blog post: [Link](https://github.com/CoffeeBeforeArch/pytest/tree/main/src/marks/01_xfail)
- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My Email: CoffeeBeforeArch@gmail.com

# Expecting Failures

Another builtin marker that is commonly used when writing tests is the `xfail` marker. `@pytest.mark.xfail` allows us say that we expect for a test to fail. Recall our simple `test_square` that we used in the [last blog post](skip.md):

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

We can make small modification to our `square` function to make it fail our `assert`:

```python
def square(num):
    return num + num
```

When we run this test, we should see the following failure:

```
cba@cba$ pytest test_functions.py::test_square
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 1 item                                                                                    

test_functions.py F                                                                           [100%]

============================================= FAILURES ==============================================
____________________________________________ test_square ____________________________________________

    def test_square():
        num = 5
        result = square(num)
>       assert result == num ** 2
E       assert 10 == (5 ** 2)

test_functions.py:18: AssertionError
====================================== short test summary info ======================================
FAILED test_functions.py::test_square - assert 10 == (5 ** 2)
========================================= 1 failed in 0.05s =========================================
```

If we add `@pytest.mark.xfail` above our tests, we can let `pytest` know that we expect this test to fail:

```python
# A simple test for our function 'square'
@pytest.mark.xfail
def test_square():
    num = 5
    result = square(num)
    assert result == num ** 2
```

When we execute the test again, we should see the following:

```
cba@cba$ pytest test_functions.py::test_square
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 1 item                                                                                    

test_functions.py x                                                                           [100%]

======================================== 1 xfailed in 0.03s =========================================
```

Instead of test being marked as failing, it is now being marked as `xfailed` (expected to fail).

We can also `xfail` a test from within the test body. For example, instead of adding `@pytest.mark.xfail` above the test, we can add `pytest.xfail()` within the test:

```python
# A simple test for our function 'square'
def test_square():
    pytest.xfail()
    num = 5
    result = square(num)
    assert result == num ** 2

```

When we run this test, we see se same result as before:

```
cba@cba$ pytest test_functions.py::test_square
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 1 item                                                                                    

test_functions.py x                                                                           [100%]

======================================== 1 xfailed in 0.02s =========================================
```

Just like with `@pytest.mark.skip`, we can specify why a test was `xfailed` using a `reason` string:

```python
# A simple test for our function 'square'
@pytest.mark.xfail(reason='There is a bug in our implementation!')
def test_square():
    num = 5
    result = square(num)
    assert result == num ** 2

```

When we execute our test with `-rx` to print the summary info for why the test was `xfailed`, we see our `reason`:

```
cba@cba$ pytest test_functions.py::test_square -rx
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 1 item                                                                                    

test_functions.py x                                                                           [100%]

====================================== short test summary info ======================================
XFAIL test_functions.py::test_square
  There is a bug in our implementation!
======================================== 1 xfailed in 0.02s =========================================
```

When we have an `xfail` test, we often care about the specific reason why the test failed. For example, we may want to ensure that we are hitting the same assertion error every time, and not failing at a different part of execution.

To our `xfail` marker, we can pass a `raises` parameter saying what kind of exception (or tuple of exceptions) we expect to see:

```python
# A simple test for our function 'square'
@pytest.mark.xfail(raises=AssertionError)
def test_square():
    num = 5
    result = square(num)
    assert result == num ** 2
```

With a `raises` exception specified to be an `AssertionError`, the test will now fail if it encounters different exception. For example, we can insert a division by zero which will lead to a `ZeroDivisionError`:

```python
# A simple test for our function 'square'
@pytest.mark.xfail(raises=AssertionError)
def test_square():
    num = 5
    num = num / 0
    result = square(num)
    assert result == num ** 2
```

When we run the test, we see that our test no longer completes as `xfailed`, but as a normal failure:

```
cba@cba$ pytest test_functions.py::test_square
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 1 item

test_functions.py F                                                                           [100%]

============================================= FAILURES ==============================================
____________________________________________ test_square ____________________________________________

    @pytest.mark.xfail(raises=AssertionError)
    def test_square():
        num = 5
>       num = 5 / 0
E       ZeroDivisionError: division by zero

test_functions.py:18: ZeroDivisionError
========================================= 1 failed in 0.04s =========================================
```

As mentioned earlier, we mark tests as with `xfail` because we expect or intend for the tests to fail. In these situations, we may want to consider an `xfailed` test passing as a failure. To do this, we can add the `strict` argument to our `xfail` marker.

By setting `strict=True`, we're saying that we expect the test to fail, and if it passes, consider that a failure:

```python
# A simple test for our function 'square'
@pytest.mark.xfail(strict=True)
def test_square():
    num = 5
    result = square(num)
    assert result == num ** 2
```

If we fix the bug in our `square` implementation and re-run `test_square`, we should see the following failure:

```
cba@cba$ pytest test_functions.py::test_square
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 1 item

test_functions.py F                                                                           [100%]

============================================= FAILURES ==============================================
____________________________________________ test_square ____________________________________________
[XPASS(strict)]
====================================== short test summary info ======================================
FAILED test_functions.py::test_square
========================================= 1 failed in 0.01s =========================================
```

We see that our our test has unexpectedly passed (`XPASS`) which is considered a failure.

The final param we can add to our `xfail` marker is `run`. `run` allows us to control whether or not we run an `xfailed` test or skip it entirely. For example, we can set `run=False` in our `test_square` example to have the testmarked as `xfailed` but not execute it:

```python
# A simple test for our function 'square'
@pytest.mark.xfail(run=False)
def test_square():
    num = 5
    result = square(num)
    assert result == num ** 2
```

When we run the test, we see that the test is still `xfailed`, but that the reason is specified as `[NOTRUN]`:

```
cba@cba$ pytest test_functions.py::test_square -rx
======================================== test session starts ========================================
platform linux -- Python 3.9.7, pytest-7.1.1, pluggy-1.0.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/intro/00_intro
plugins: benchmark-3.2.2
collected 1 item

test_functions.py x                                                                           [100%]

====================================== short test summary info ======================================
XFAIL test_functions.py::test_square
  reason: [NOTRUN]
======================================== 1 xfailed in 0.03s =========================================
```

# Conclusion

Failures are inevitable in testing. Using `xfail`, we can control how we handle failing tests, track specific exceptions, and skip tests if needed. In future blog posts, we'll continue our look into the builtin marks of `pytest` with `parametrize`.

Cheers,

--Nick

## Contact Information

- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My GitHub: [Link](https://github.com/CoffeeBeforeArch)
- My Email: CoffeeBeforeArch@gmail.com

