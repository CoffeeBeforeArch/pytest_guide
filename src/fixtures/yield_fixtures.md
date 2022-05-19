# `pytest` `yield` Fixtures

In the past few blog posts, we've talked about how fixtures can be used for test setup and providing values to a test. However, there times where we want a fixture perform some setup actions before a test then perform some teardown actions after the test executes.

I this blog post, we'll be looking at how we can do this with `yield` fixtures.

- Link to pytest documentation: [Link](https://docs.pytest.org/en/7.1.x/)

## Links

- Code from this blog post: [Link](https://github.com/CoffeeBeforeArch/pytest/tree/main/src/fixtures/03_yield_fixtures)
- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My Email: CoffeeBeforeArch@gmail.com

# `pytest` Fixtures

Recall our simple test from the past few examples:

```python
# A simple function that squares a number
def square(num):
    return num * num

# A simple test for our function 'square'
def test_square(num):
    result = square(num)
    assert result == num ** 2

```

Our test uses a simple fixture, `num`, that provides the number `5` to our test.

```python
@pytest.fixture
def num():
    return 5
```

But what if we wanted our test fixture to also to do something when the test exits? For this, we can use `yield` fixtures.

`yield` fixtures allow us to run a fixture before before a test starts, and the resume the fixture after the test ends. For example, let's change our fixture to print out a string before we `yield` a value, then print out another string after the fixture resumes:

```python
@pytest.fixture
def num():
  print('Providing a value to our test!')
  yield 5
  print('Finishing up!')
```

Instead of calling return, our function now calls `yield` so it can be resumed after our test finishes. Additionally, we can add a print to our test to show that our fixture prints our outside of test execution:

```python
# A simple test for our function 'square'
def test_square(num):
    print("Running our test!")
    result = square(num)
    assert result == num ** 2

```

When we run our test, we see the following:

```
cba@cba$ pytest test_yield_fixtures.py -s
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/fixtures/03_yield_fixture
plugins: benchmark-3.2.2
collected 1 item                                                                                    

test_yield_fixtures.py Providing a value to our test!
Running our test!
.Finishing Up!


========================================= 1 passed in 0.00s =========================================
```

We see all three strings in the order we expect! Our string before `yield` in our fixture is printed before the string from our test body, which is printed before the string after the `yield` in our fixtures.

# Conclusion

`yield` fixtures are incredibly useful when we need to acquire a resource for our test, but also free that resource when the test completes.

In future posts, we'll be looking at some of the other more advanced ways we can configure `pytests`.

Cheers,

--Nick

## Contact Information

- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My GitHub: [Link](https://github.com/CoffeeBeforeArch)
- My Email: CoffeeBeforeArch@gmail.com

