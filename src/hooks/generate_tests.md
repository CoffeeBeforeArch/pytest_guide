# Dynamic Parametrization in `pytest`

`pytest.mark.parametrize` is useful for statically parametrizing tests. However, there are times where we want to parametrize a test at runtime based on something something at runtime (e.g., a flag).

In this blog post, we'll look at the basic of dynamic parametrization in `pytest` using hook `pytest_generate_tests`.

- Link to pytest documentation: [Link](https://docs.pytest.org/en/7.1.x/)

## Links

- Code from this blog post: [Link](https://github.com/CoffeeBeforeArch/pytest/tree/main/src/hooks/00_generate_tests)
- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My Email: CoffeeBeforeArch@gmail.com

# Test Parametrization

Recall our simple test from our [blog post](../markers/parametrize.md) on parametrization:

```python
# A simple test for our function 'square'
@pytest.mark.parametrize('num', [1, 2, 3, 4, 5])
def test_square(num):
    result = square(num)
    assert result == num ** 2
```

Here, we were using the `@pytest.mark.parametrize` marker to create 5 variants of our test. But what if we wanted to parametrize our test based on something that happens at runtime? For this, we can use the built-in hook, `pytest_generate_tests`.

`pytest_generate_tests` is a special function called during test collection that is passed a `metafunc` object. Through this object, we can call `metafunc.parametrize()` to dynamically parametrize a test.

For example, we can replace our parametrization of `num` in our original function with a dynamically parametrized test with the following implementation:

```python
def pytest_generate_tests(metafunc):
    if 'num' in metafunc.fixturenames:
        metafunc.parametrize('num', [1, 2, 3, 4, 5])
```

Here, our function is checking for a fixture named `num` in the parameter list of our test, and if it exists, adds a parametrization for the name `num`, to our test. Here's what our updated test looks like:

```python
# A simple test for our function 'square'
def test_square(num):
    result = square(num)
    assert result == num ** 2
```

When we run collection we see all 5 variants of our test:

```
cba@cba$ pytest --collectonly test_fixtures.py
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/hooks/00_generate_tests
plugins: benchmark-3.2.2
collected 5 items

<Module test_fixtures.py>
  <Function test_square[1]>
  <Function test_square[2]>
  <Function test_square[3]>
  <Function test_square[4]>
  <Function test_square[5]>

==================================== 5 tests collected in 0.00s =====================================
```

In many cases, we want to dynamically parametrize using some command line input.

# Conclusion


Cheers,

--Nick

## Contact Information

- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My GitHub: [Link](https://github.com/CoffeeBeforeArch)
- My Email: CoffeeBeforeArch@gmail.com

