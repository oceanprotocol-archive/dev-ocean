# Python Style Guide

Generally, follow the [Google Python Style Guide](https://github.com/google/styleguide/blob/gh-pages/pyguide.md).

Here are some updates and personal preferences from my side;

## Global imports
Prefer to import the full context of modules.

Do not import modules and classes into the global namespace;
```
from pandas import DataFrame, Series
df = DataFrame()
s1 = Series()
```

Instead, keep the context of the package;
```
import pandas as pd
df = pd.DataFrame()
s1 = pd.Series()
```

## New 3.6 style string formatting
Python 3.6.0 implements [PEP 498](https://docs.python.org/3/whatsnew/3.6.html#whatsnew36-pep498),
inline string literals. For now, stick with the `.format()` formatter.
Prefer `.format()` vs `%` formatting (so explicitly decide on sec. [3.10](https://github.com/google/styleguide/blob/gh-pages/pyguide.md#310-strings))

## "a string" vs 'string'
Prefer `''` for dictionary keys. `my_dict['key1']`

Prefer `""` for all other cases. `fname = "data.csv"`

