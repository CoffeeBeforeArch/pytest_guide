# `pytest` Marks

There are times that we expect our tests to fail. This may be because we have written our tests before the implementation of some feature is done, or because we are specifically testing a failure path in our code.

In this blog post, we'll be looking at how to use the `pytest.mark.xfail` marker to mark teststhat we expect to fail.

- Link to pytest documentation: [Link](https://docs.pytest.org/en/7.1.x/)

## Links

- Code from this blog post: [Link](https://github.com/CoffeeBeforeArch/pytest/tree/main/src/marks)
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

We can make small modification to our `square` function to make it fail the `assert`:

```python
def square(num):
    return num + num
```

Then, when we run this test, we should see the following failure:

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

We can then add `@pytest.mark.xfail` avove our tests to let `pytest` know that we expect this test to fail.

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

Instead of test being marked as failing, it is now being marked as `xfailed`.

We can also `xfail` a test from within the test body. For example, instead of adding `@pytest.mark.xfail` above the test, we can add `pytest.xfail()` within the test:

```python
# A simple test for our function 'square'
def test_square():
    pytest.xfail()
    num = 5
    result = square(num)
    assert result == num ** 2

```

# Conclusion

Cheers,

--Nick

## Contact Information

- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My GitHub: [Link](https://github.com/CoffeeBeforeArch)
- My Email: CoffeeBeforeArch@gmail.com

