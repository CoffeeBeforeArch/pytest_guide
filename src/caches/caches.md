# Introduction to `pytest` Caches

When we re-run tests, we often generate the same inputs with our test fixtures that we did before. While we can't directly re-use inputs from a prior test run using just fixtures, we can with the help of caches.

In this blog post, we'll be looking at the basics of using `pytest` caches.

- Link to pytest documentation: [Link](https://docs.pytest.org/en/7.1.x/)

## Links

- Code from this blog post: [Link](https://github.com/CoffeeBeforeArch/pytest/tree/main/src/fixtures/00_basic_fixtures)
- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My Email: CoffeeBeforeArch@gmail.com

# `pytest` Caches

Consider a simple fixture that generates a an input value using an expensive method:

```python
@pytest.fixture
def value():
    value = generate_expensive_value()
    return value
```

If we have multiple tests in the file that need this same input, we could change the `scope` of our fixture so that it is invoked only once per module:

```python
@pytest.fixture(scope='module')
def value():
    value = generate_expensive_value()
    return value
```

While this allows us to get some re-use out of our expensive value within a testrun, we often are generating the same values across testruns.

It's often the case that we will run a test multiple times during the development process of some feature. Each time we run the test, this fixture gets invoked again, and has to generate this expensive value. Fortunately, we can cache values across `pytest` invocations using `config.cache`.

To use this cache, we need to add the bultin [request](https://docs.pytest.org/en/7.1.x/reference/reference.html#std-fixture-request) fixture to our fixture:

```python
@pytest.fixture(scope='module')
def value(request):
    value = generate_expensive_value()
    return value
```

The `request` fixture provides information about the requesting test function. What we're going to be using specifically from this fixture is `request.config.cache`. Specifically, we'll be calling `get` to check if our value is in the cache, and `set` to place our value in the cache (if it is not already there).

Let's modify our fixture to cache the expensive value if it is not already in the cache:

```python
@pytest.fixture(scope='module')
def value(request):
    value = request.config.cache.get('expensive_value', None)
    if not value:
        print('Generating expensive value!')
        value = generate_expensive_value()
        request.config.cache.set('expensive_value', value)
    return value
```

Our fixture now checks our cache for `expensive_value`. If it exists in our cache, our fixture simply returns the value loaded from the cache. If it doesn't exist in the cache, we generate the value, set it in our cache (for later pytest runs), then return it.

Let's use our fixture in our simple `test_square` test we've been looking at over the past few blog posts:

```python
# A simple test for our function 'square'
def test_square(value):
    result = square(value)
    assert result == value ** 2
```

When we run our test for the first time, we get the following:

```
cba@cba$ pytest --cache-clear test_caches.py -s
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/caches/00_basic_caches
plugins: benchmark-3.2.2
collected 1 item

test_caches.py Generating expensive value!
.

========================================= 1 passed in 0.00s =========================================
```

On the first run, the `expensive_value` isn't in our cache yet so we see our print saying that we're generating the expensive value. However, if we run the test again, we get the following:

```
cba@cba$ pytest test_caches.py -s
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/caches/00_basic_caches
plugins: benchmark-3.2.2
collected 1 item

test_caches.py .

========================================= 1 passed in 0.00s =========================================
```

No print! Our fixture used the value that was previously stored in the cache instead of generating a new one!

We can inspect the cache using `pytest --cache-show=<VALUE NAME>`. For example, we can replace `<VALUE NAME>` with expensive value to see what our `expensive_value` is in our cache:

```
cba@cba$ pytest --cache-show=expensive_value
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/caches/00_basic_caches
plugins: benchmark-3.2.2
cachedir: /home/cba/forked_repos/pytest/src/caches/00_basic_caches/.pytest_cache
-------------------------------- cache values for 'expensive_value' ---------------------------------
expensive_value contains:
  6

======================================= no tests ran in 0.00s =======================================
```

We see our expensive value is actually the number `6`! If we wanted to force our fixture to generat a new value, we can use the `--cache-clear` option when running our test:

```
cba@cba$ pytest --cache-clear test_caches.py -s
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/caches/00_basic_caches
plugins: benchmark-3.2.2
collected 1 item

test_caches.py Generating expensive value!
.

========================================= 1 passed in 0.00s =========================================
```

We see our print indicating that we are generating a new `expensive_value`!

# Conclusion

`pytest` caches provide a simple way to re-use value across testruns. In future blog posts, we'll be looking at some of the other advanced ways we can use `pytest`.

Cheers,

--Nick

## Contact Information

- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My GitHub: [Link](https://github.com/CoffeeBeforeArch)
- My Email: CoffeeBeforeArch@gmail.com

