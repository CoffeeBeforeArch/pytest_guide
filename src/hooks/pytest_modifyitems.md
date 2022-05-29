# Modifying Collection in `pytest`

There are times we want to modify tests after they have been collected. For example, we may want to add additional markers, or deselect tests. For this we can use the builtin `pytest` hook `pytest_collection_modifyitems`.

In this blog post, we'll be looking how to modify tests after collection in `pytest`.

- Link to pytest documentation: [Link](https://docs.pytest.org/en/7.1.x/)

## Links

- Code from this blog post: [Link](https://github.com/CoffeeBeforeArch/pytest/tree/main/src/hooks/02_collection_modifyitems)
- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My Email: CoffeeBeforeArch@gmail.com

# The `pytest_collection_modifyitems` Hook

Consider our simple `pytest` `test_square` from the past few blog posts:

```python
# Simple function that squares a number
def square(num):
    return num * num


# One test that uses our fixture
@pytest.mark.parametrize("num", range(10))
def test_square(num):
    result = square(num)
    assert result == num ** 2
```

There are times we want to skip some of our tests after they have been collected. For this, we can us the hook `pytest_collection_modifyitems` which is called after collection.

For example, we can implement a simple hook that skips every other test:

```python
def pytest_collection_modifyitems(items, config):
    # Even tests are selected, odds are deselected
    for i, item in enumerate(items):
        if i % 2:
            item.add_marker(pytest.mark.skip(reason="Skipping every other test!"))
```

For every other test, we're adding an additional `pytest.mark.skip` marker to skip the test. When we run all our tests, we get the following:

```
cba@cba$ pytest test_modify_collection.py 
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/hooks/02_collection_modifyitems/00_basics
plugins: benchmark-3.2.2
collected 10 items                                                                                  

test_modify_collection.py .s.s.s.s.s                                                          [100%]

=================================== 5 passed, 5 skipped in 0.01s ====================================
```

Half of our tests run, while the other half (marked with `pytest.mark.skip` after collection) are skipped.

In many cases, we will want to modify collection based on how a test is parametrized. For example, there may be certain combinations of inputs that are invalid that we want to filter out.

We can access the values of items through `callspec`. For example, here's the example of the `pytest_collection_modifyitems` hook where deselect a test if the value of `num` is `1` or `5`.:

```python
def pytest_collection_modifyitems(items, config):
    selected = []
    deselected = []
    for item in items:
        # Get the value from the callspec
        num = item.callspec.params.get("num")
        
        # Add 1 and 5 to deselected list
        if num in [1, 5]:
            deselected.append(item)
        else:
            selected.append(item)

    # Update the deselected tests
    config.hook.pytest_deselected(items=deselected)

    # Update the selected tests
    items[:] = selected
```

Here, we access the dictionary under `item.callspec.params` to get the values of the input parameters to our test variants. To update which tests are deselected, we can add the deselected test list to `config.hook.pytest_deselected`. To updated the selected tests, we simply modify the `items` list passed to the hook.

When we run collection for our test, we get the following:

```
cba@cba$ pytest --collectonly test_modify_collection.py
======================================== test session starts ========================================
platform linux -- Python 3.10.4, pytest-6.2.5, py-1.10.0, pluggy-0.13.0
benchmark: 3.2.2 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/cba/forked_repos/pytest/src/hooks/02_collection_modifyitems/01_deselect
plugins: benchmark-3.2.2
collected 10 items / 2 deselected / 8 selected

<Module test_modify_collection.py>
  <Function test_square[0]>
  <Function test_square[2]>
  <Function test_square[3]>
  <Function test_square[4]>
  <Function test_square[6]>
  <Function test_square[7]>
  <Function test_square[8]>
  <Function test_square[9]>

=========================== 8/10 tests collected (2 deselected) in 0.00s ============================
```

Here, we see that `pytest` initiall collected `10` items, but `2` tests (with input `1` and input `5`) are then deselected with our hook.

# Conclusion

`pytest_collection_modifyitems` provides us with a useful way to modify the use of `pytest` collection after the fact. In future blog posts, we'll be looking at some of the other ways we can use builtin `pytest` hooks.

Cheers,

--Nick

## Contact Information

- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My GitHub: [Link](https://github.com/CoffeeBeforeArch)
- My Email: CoffeeBeforeArch@gmail.com

