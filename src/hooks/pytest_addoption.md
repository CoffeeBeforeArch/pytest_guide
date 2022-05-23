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

# Conclusion


Cheers,

--Nick

## Contact Information

- My YouTube Channel: [Link](https://www.youtube.com/coffeebeforearch)
- My GitHub: [Link](https://github.com/CoffeeBeforeArch)
- My Email: CoffeeBeforeArch@gmail.com

