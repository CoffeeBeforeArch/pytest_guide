# Forwarding Parameters to `pytest` Fixtures

In practice, our test setup is often dependent on how a test is parametrized. For example, tests may be parametrized with input file names, and each test variant may want to read data from a different file.

In this blog post, we'll be looking at how we can forward test parameters to test fixtures.

- Link to pytest documentation: [Link](https://docs.pytest.org/en/7.1.x/)

## Links

- Code from this blog post: [Link](https://github.com/CoffeeBeforeArch/pytest/tree/main/src/fixtures/02_param_forwarding)
- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My Email: CoffeeBeforeArch@gmail.com

# `pytest` Fixtures

Recall our simple parametrized test from [an earlier blog post](../marks/parametrize.md):

```python
# A simple function that squares a number
def square(num):
    return num * num

# A simple test for our function 'square'
@pytest.mark.parametrize('num', [1, 2, 3, 4, 5])
def test_square(num):
    result = square(num)
    assert result == num ** 2
```

Here, we're testing our function `square` on each of the values specified for `num`.

Let's say we want each test to print the value of `num` before it runs. Very simply, we could add a print to our test that does just that:

```python
# A simple test for our function 'square'
@pytest.mark.parametrize('num', [1, 2, 3, 4, 5])
def test_square(num):
    print(f'The value of num is {num}')
    result = square(num)
    assert result == num ** 2
```

When we run the test, we get the following:

```
cba@cba$ pytest test_param_fowarding.py -s
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/fixtures/02_param_forwarding
plugins: benchmark-3.2.2
collected 5 items

test_param_fowarding.py The value of num is 1
.The value of num is 2
.The value of num is 3
.The value of num is 4
.The value of num is 5
.

========================================= 5 passed in 0.01s =========================================
```

As expected, we see a print out for each value of `num`. However, we have introduced some test setup code into our test function. While this might be ok for a trivial example, we should prefer (in general) to have our test setup handled by test fixtures, and keep our test functions largely free of setup code.

Let's move our logging to a test fixture. We can write our logging fixtures as follows:

```python
@pytest.fixture
def log_num(num):
    print(f"The value of num is {num}")
```

We know that a test can request a fixture by adding the fixture name to its parameter list, but how does a test function call a fixture using an argument?

Simple! It happens automatically! As long as the parameter list for the test contains the same named parameter that the fixture requires, it will automatically be forwarded!

For example, let's update our test to use this new fixture:

```python
# A simple test for our function 'square'
@pytest.mark.parametrize('num', [1, 2, 3, 4, 5])
def test_square(num, log_num):
    result = square(num)
    assert result == num ** 2
```

When we run the test, we get the identical result as before and avoided having setup code in our test body:

```
cba@cba$ pytest test_param_fowarding.py -s
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/fixtures/02_param_forwarding
plugins: benchmark-3.2.2
collected 5 items

test_param_fowarding.py The value of num is 1
.The value of num is 2
.The value of num is 3
.The value of num is 4
.The value of num is 5
.

========================================= 5 passed in 0.01s =========================================
```

It is also possible to forward parameters to fixtures that have not been requested directly (i.e., `autouse` fixtures). For example, we can remove `log_num` from the parameter list of `test_square`, and add `autouse=True` to our logging fixture `log_num`:


```python
@pytest.fixture(autouse=True)
def log_num(num):
    print(f"The value of num is {num}")

# A simple test for our function 'square'
@pytest.mark.parametrize('num', [1, 2, 3, 4, 5])
def test_square(num):
    result = square(num)
    assert result == num ** 2
```

If we run the test, we again see the same output:

```
cba@cba$ pytest test_param_fowarding.py -s
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/fixtures/02_param_forwarding
plugins: benchmark-3.2.2
collected 5 items

test_param_fowarding.py The value of num is 1
.The value of num is 2
.The value of num is 3
.The value of num is 4
.The value of num is 5
.

========================================= 5 passed in 0.01s =========================================
```

Note, we are implicitly relying on our test having a parameter named `num` that can be forwarded to the fixture. If for some reason we did not have `num` in our test's parameter list, we would get an error like the following:

```
_________________________________ ERROR at setup of test_square[1] __________________________________
file /home/cba/forked_repos/pytest/src/fixtures/02_param_forwarding/test_param_fowarding.py, line 16
  @pytest.mark.parametrize("num1", [1, 2, 3, 4, 5])
  def test_square(num1):
file /home/cba/forked_repos/pytest/src/fixtures/02_param_forwarding/test_param_fowarding.py, line 6
  @pytest.fixture(autouse=True)
  def log_num(num):
E       fixture 'num' not found
>       available fixtures: benchmark, benchmark_weave, cache, capfd, capfdbinary, caplog, capsys, capsysbinary, doctest_namespace, expensive_input, log_num, monkeypatch, pytestconfig, record_property, record_testsuite_property, record_xml_attribute, recwarn, tmp_path, tmp_path_factory, tmpdir, tmpdir_factory
>       use 'pytest --fixtures [testpath]' for help on them.

```

`pytest` was unable to find a fixture (or parameter) named `num` that `log_num` was requiring (so be careful about `autouse` fixtures that require forwarded parameters!).

# Conclusion

Forwarding parameters allows to to configure parametrized tests in different ways (based on their inputs). In future blog posts, we'll be continuing our look at the more advanced ways in which we can used test fixtures.

Cheers,

--Nick

## Contact Information

- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My GitHub: [Link](https://github.com/CoffeeBeforeArch)
- My Email: CoffeeBeforeArch@gmail.com

