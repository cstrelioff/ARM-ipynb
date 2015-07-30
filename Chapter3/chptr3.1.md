# 3.1: One predictor

```python
from __future__ import print_function, division
%matplotlib inline

import matplotlib
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
# use matplotlib style sheet
plt.style.use('ggplot')

# import statsmodels for R-style regression
import statsmodels.formula.api as smf
```

## Read the data

Data are in the *child.iq* directory of the ARM_Data download-- you might have
to change the path I use below to reflect the path on your computer.

```python
kidiq  = pd.read_stata("../../ARM_Data/child.iq/kidiq.dta")
kidiq.head()
```

## First regression-- binary predictor, Pg 31

Fit the regression using the non-jittered data

```python
fit0 = smf.ols('kid_score ~ mom_hs', data=kidiq).fit()
print(fit0.summary())
```

## Plot Figure 3.1, Pg 32

A note for the python version:

* I have **not** included jitter, in the vertical or horizontal directions.
  Instead, the data is plotted with opacity so the regions with high
  data-density can be distinguished.

```python
fig0, ax0 = plt.subplots(figsize=(8, 6))
hs_linspace = np.linspace(kidiq['mom_hs'].min(), kidiq['mom_hs'].max(), 50)

# default color cycle
colors = plt.rcParams['axes.color_cycle']

# plot points
plt.scatter(kidiq['mom_hs'], kidiq['kid_score'],
            s=60, alpha=0.5, c=colors[1])
# add fit
plt.plot(hs_linspace, fit0.params[0] + fit0.params[1] * hs_linspace,
         lw=3, c=colors[1])

plt.xlabel("Mother completed high school")
plt.ylabel("Child test score")
```

## Second regression -- continuous predictor, Pg 32

```python
fit1 = smf.ols('kid_score ~ mom_iq', data=kidiq).fit()
print(fit1.summary())
```

## Figure 3.2, Pg 33

```python
fig1, ax1 = plt.subplots(figsize=(8, 6))
iq_linspace = np.linspace(kidiq['mom_iq'].min(), kidiq['mom_iq'].max(), 50)

# default color cycle
colors = plt.rcParams['axes.color_cycle']

# plot points
plt.scatter(kidiq['mom_iq'], kidiq['kid_score'],
            s=60, alpha=0.5, c=colors[1])
# add fit
plt.plot(iq_linspace, fit1.params[0] + fit1.params[1] * iq_linspace,
         lw=3, c=colors[1])

plt.xlabel("Mother IQ score")
plt.ylabel("Child test score")
```
