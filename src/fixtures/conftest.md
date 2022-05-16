# `pytest` Fixtures in `conftest.py`

In the [previous blog post](basic_fixtures.md), we looked at the basics of test fixtures in `pytest`. However, we neglected to answer the question of where these fixtures should live!

In this blog post, we'll be looking at a special file in `pytest` called `conftest.py` where we can store our test fixtures. 

- Link to pytest documentation: [Link](https://docs.pytest.org/en/7.1.x/)

## Links

- Code from this blog post: [Link](https://github.com/CoffeeBeforeArch/pytest/tree/main/src/intro/00_intro)
- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My Email: CoffeeBeforeArch@gmail.com

# The `conftest.py` File

In the [previous blog post](basic_fixtures.md), we wrote a simple `pytest` fixture that returned the number `5`:

```python
@pytest.fixture
def num():
    return 5
```

We placed this fixture in the same file as our test (`test_square`). While this is fine for our trivial example, it's not necessarily a good choice in general. In practice, we  may have tests located in different files that want to use the same fixture.

An intuitive solution would be to separate the tests from our fixtures. Fortunately, `pytest` provides a nice way to do this without needing to import the fixtures in every test file. The solution `pytest` provides is a special file called `conftest.py`.

`conftest.py` is a file that `pytest` will search for test fixtures automatically. We can add a `conftest.py` file to the directory with our test and place our test fixture `num` in the file. Inside our test file, we now only have our test function (`test_square`) and the function we are testing (`square`):

```python
# A simple test for our function 'square'
def test_square(num):
    result = square(num)
    assert result == num ** 2
```

When we run the test, we see that our test is collected, runs, and passes:

```
cba@cba$ pytest test_fixtures.py::test_square
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/fixtures/00_basics_fixtures
plugins: benchmark-3.2.2
collected 1 item                                                                                    

test_fixtures.py .                                                                            [100%]

========================================= 1 passed in 0.01s =========================================
```

`pytest` was able to find our requested fixture (`num`) in our `conftest.py` file without needing a dedicated `import`!

`pytest` also supports having multiple `conftest.py` files in nested sub-directories. For example, we can move our test file to a sub-directory below our original `conftest.py` file. We can then add a new `conftest.py` file in the new directory containing our test.

The new directory structure should look like this:

```
base_directry
|    conftest.py
|
└────nested_directory
     |    test_functions.py
     |    conftest.py
```

Inside our second `conftest.py` we can add an add our `autouse` fixture from the last blog post that simply logs that the test is starting:

```python
@pytest.fixture(autouse=True)
def log_start():
    print('Test Starting!')
```

When we run our test, we get the following:

```
cba@cba$ pytest test_fixtures.py::test_square -s
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/fixtures/00_basics_fixtures/subdir
plugins: benchmark-3.2.2
collected 1 item

test_fixtures.py Test Starting!
.

========================================= 1 passed in 0.01s =========================================
```

Two notable things happened:

1. `pytest` picked up our `autouse` fixture from the `conftest.py` file in the same directory of the test
2. `pytest` still found our `num` fixture in the `conftest.py` file in the parent directory.

There are times where it makes sense to use the same name for fixtures in different `conftest.py` files. For example, a top-level `conftest.py` file may implement a generic form of some fixture, while `conftest.py` files in child directories implement something more specific.

We can test this by implementing a new version of the `num` fixture in the `conftest.py` file in our sub-directory with the test. Our new fixture will provide a different number (`10`) and print that it is being used:

```python
@pytest.fixture
def num():
    print('Providing the value 10!')
    return 10
```

When we run the test, we get the following output:

```
cba@cba$ pytest test_fixtures.py::test_square -s
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/fixtures/00_basics_fixtures/subdir
plugins: benchmark-3.2.2
collected 1 item                                                                                    

test_fixtures.py Providing the number 10!
.

========================================= 1 passed in 0.01s =========================================
```

Instead of picking up our `num` fixture that returns the value `5`, `pytest` chose the fixture that prints our string and returns the value `10`! `pytest` will select the test fixture from the closest `conftest.py` file to the test (in our case, the one in the same directory as the test).

# Conclusion

`conftest.py` helps reduce the burden on test authors when writing and using fixtures. In future blog posts, we'll continue at looking at some of the more advanced ways we can use and manage fixtures in `pytest`

Cheers,

--Nick

## Contact Information

- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My GitHub: [Link](https://github.com/CoffeeBeforeArch)
- My Email: CoffeeBeforeArch@gmail.com

