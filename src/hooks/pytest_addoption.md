# Adding Command Line Options in `pytest`

There are often times where we want to set a value at runtime. For normal Python scripts, we can use something like `argparse` to create command line options. For `pytest`, we can use the `pytest_addoption` initialization hook.

In this blog post, we'll be looking how at how to add `argparse`-like command line options using `pytest_addoption`.

- Link to pytest documentation: [Link](https://docs.pytest.org/en/7.1.x/)

## Links

- Code from this blog post: [Link](https://github.com/CoffeeBeforeArch/pytest/tree/main/src/hooks/00_pytest_addoption)
- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My Email: CoffeeBeforeArch@gmail.com

# The `pytest_addoption` Hook

The `pytest_addoption` hook allows us to add command line options to `pytest` tests. This hook runs once at the beginning of a test run, and registers our custom options.

For example, we can define a simple option named `name` that defaults to storing the string `Nick`:

```python
# Adds the argparse-like option
def pytest_addoption(parser):
    parser.addoption(
        '--name',
        action='store',
        default='Nick',
        help='Choose a name!'
    )
```

To access the value from this option, we can add the built-in `request` fixture to the paremeter list of one of our tests or fixtures. For example, let's create a special fixture to simply return the `name` option:

```python
@pytest.fixture
def name(request):
    return request.config.getoption('--name')
```

To test this option, we can write a simple test that ensures that the set value is not `Nick`:

```python
def test_name(name):
    assert name != "Nick"
```

When we run our test without specifying any options, we get the following:

```
cba@cba$ pytest test_name.py 
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/hooks/00_pytest_adoption
plugins: benchmark-3.2.2
collected 1 item                                                                                    

test_name.py F                                                                                [100%]

============================================= FAILURES ==============================================
_____________________________________________ test_name _____________________________________________

name = 'Nick'

    def test_name(name):
>       assert name != "Nick"
E       AssertionError: assert 'Nick' != 'Nick'

test_name.py:6: AssertionError
====================================== short test summary info ======================================
FAILED test_name.py::test_name - AssertionError: assert 'Nick' != 'Nick'
========================================= 1 failed in 0.07s =========================================
```

However, when we set `--name` to be something else (e.g., `Bob`). We see that our test passes:

```
cba@cba$ pytest test_name.py --name Bob
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/hooks/00_pytest_adoption
plugins: benchmark-3.2.2
collected 1 item

test_name.py .                                                                                [100%]

========================================= 1 passed in 0.01s =========================================
```

Our test passes!

# Conclusion

Adding command line options can be incredibly helpful when we want to dynamically configure a test from the command line. In future blog posts, we'll look at more ways in which we can modify test behavior at runtime.

Cheers,

--Nick

## Contact Information

- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My GitHub: [Link](https://github.com/CoffeeBeforeArch)
- My Email: CoffeeBeforeArch@gmail.com

