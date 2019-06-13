# Python Style Guide

Generally, follow the [Google Python Style Guide](https://github.com/google/styleguide/blob/gh-pages/pyguide.md).

Here are some differences:

## Linter

Most of the Ocean repositories containing Python code have [Flake8](http://flake8.pycqa.org/en/latest/) set up as the default linter for checking Python code style.

We use Codacy to check the code style of all pull requests, and Codacy uses [PyLint](https://www.pylint.org/) to check Python code style.

## Docstrings

We aren't super-strict about Python docstring formatting, but we try to follow the "reStructuredText Docstring Format" which is also known as the "Sphinx format" because it's the default format supported by Sphinx. We _don't_ use Google-style or NumPy-style docstrings. Some references:

- [Sphinx-RTD-Tutorial on writing docstrings](https://sphinx-rtd-tutorial.readthedocs.io/en/latest/docstrings.html)
- [PyCharm's help with documenting source code](https://www.jetbrains.com/help/pycharm/documenting-source-code.html)
- [Python Developer's Guide - Documenting Python](https://devguide.python.org/documenting/)
- [PEP 287 - reStructuredText Docstring Format](https://www.python.org/dev/peps/pep-0287/)

## Global Imports

Do not import modules and classes into the global namespace:

```python
from pandas import DataFrame, Series
df = DataFrame()
s1 = Series()
```

Instead, keep the context of the package:

```python
import pandas as pd
df = pd.DataFrame()
s1 = pd.Series()
```

## New Python 3.6-Style String Formatting

Python 3.6.0 implements [PEP 498](https://docs.python.org/3/whatsnew/3.6.html#whatsnew36-pep498),
inline string literals. For now, stick with the `.format()` formatter.
Prefer `.format()` vs `%` formatting (so explicitly decide on sec. [3.10](https://github.com/google/styleguide/blob/gh-pages/pyguide.md#310-strings))

## "string" vs 'string'

Prefer `''` for dictionary keys. `my_dict['key1']`

Prefer `""` for all other cases. `fname = "data.csv"`
