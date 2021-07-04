---
layout: post
title: Multiple file data loading with pyarrow
featured-img: archery
image: archery
category: []
mathjax: true
summary: Loading a dataset from disk, feature by feature with pyarrow
---

# Multiple file data loading with pyarrow

Sometimes it is preferred to store single columns of a pandas DataFrame to disk in order to reload them on demand later on.
This could be the case in a feature selection step where only a subset of all previously engineered features shall be selected.
Instead of loading a huge dataset with all engineered features into memory, one could pick only the requested features and save RAM space.

To even further improve RAM usage, the pyarrow package can be used. A dataset in pyarrow format is stored very efficiently in the RAM and can be easily converted
to a pandas DataFrame.

The following example shows how to load only hand-selected features from disk via pyarrow.

```python
import os
from pathlib import Path

import pandas as pd
import pyarrow.parquet as pq

# Make folder to store features
storage_folderpath = Path(os.path.join(os.getcwd(), 'features'))
storage_folderpath.mkdir(parents=True, exist_ok=True)

# Build data
data = pd.DataFrame({
    'categoricals': ['A', 'B', 'C', 'D'],
    'floats': [0.1, 0.2, 0.3, 0.4],
    'integers': [1, 2, 3, 4]
},
index=['Row1', 'Row2', 'Row3', 'Row4'])

# Columns that shall be part of the final disk-loaded data
requested_columns = ['categoricals', 'integers']

# Store each column in its own parquet file
filepaths = {}
for col in data.columns:
    output_filepath = os.path.join(storage_folderpath, col + ".parquet")
    data[[col]].to_parquet(output_filepath)
    filepaths.update({col: output_filepath})

# Load requested features via pyarrow
requested_filepaths = {key: value for key, value in filepaths.items() if key in requested_columns}
tables_combined = None
for filepath in requested_filepaths.values():

    table = pq.read_table(filepath)

    if tables_combined is not None:
        # select first column, which contains the feature values. The last column contains the table's indices
        table_values = table[0]
        table_name = table.field(0)

        tables_combined = tables_combined.append_column(table_name, table_values)
    else:
        tables_combined = table

print(f"converting pyarrow table to pandas")
data = tables_combined.to_pandas()

print(data)
```