---
layout: post
title: Multidimensional Indexing with Xarray
featured-img: sleek
image: sleek
category: []
mathjax: true
summary: How to obtain pandas-like indexing on multidimensional data using xarray
---

# Multidimensional Indexing with xarray

When working with multidmensional data (e.g. lstm data or geospatial data) the pandas package is not helpful anymore, since its DataFrames
   only support 2d datasets. In order to still maintain indexing functionality, the xarray package can be used.

In this snippet an lstm-like time series dataset is created and indexed via xarray

```python
import numpy as np
import xarray as xr

# Create a random time series dataset with
# 1. dimension: observations, each new row is an observation at a new time t = t_-1 + dt
# 2. dimension: previous time_steps that are taken into account during modelling for each individual observation
# 3. dimension: features that are taken into account during modelling for each individual observation

n_observations = 100
n_time_steps = 3
n_features = 2

observation_indices = list(range(n_observations))
time_step_indices = list(range(n_time_steps))
feature_indices = ['feature1', 'feature2']

# Create 2d time series data
data_xr = xr.DataArray(np.random.rand(n_observations, n_features),
                       dims=('observation', 'feature'),
                       coords={
                           'observation': observation_indices,
                           'feature': feature_indices,
                       })

# Initialise a 3d lstm-like DataArray only filled with NaNs
lstm_data_xr = xr.DataArray(
                       dims=('observation', 'time_step', 'feature'),
                       coords={
                           'observation': observation_indices,
                            'time_step': time_step_indices,
                           'feature': feature_indices,
                       })

# Iterate over the 'time_steps' dimension and fill the lstm data values by shifting the observation dimension
for tt in range(n_time_steps):
    lstm_data_xr.loc[:, tt, :] = data_xr.shift(observation=tt)


# Exemplary data retrieval by index

# specific value: 2nd observation, first time step, first feature
print("")
print("specific value: ", lstm_data_xr.loc[1, 0, 'feature1'])

# extracting one dimension: 2nd observation, all time steps, first feature
print("")
print("full dimension: ", lstm_data_xr.loc[1, :, 'feature1'])

# First three observations:
print("")
print("First three observations: ", lstm_data_xr.loc[:2, :, :])
```
