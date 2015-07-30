# 3.4 Statistical inference

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

## Regression-- to demonstrate reports of fit, Pg38

```python
fit = smf.ols('kid_score ~ mom_hs + mom_iq', data=kidiq).fit()
```

## Display, Pg 38

There is no python counterpart to the *display* function in R. However, we can
quickly write one for use-- the idea here is to provide an intermediate amount
of information, preferred by the authors, about the fit.

```python
def display(f):
    """Replicate R-style display command."""
    
    output = "{:<12s}  {:>10s}   {:>10s}\n".format("", "coef.est", "coef.se")
    for p in fit.bse.index:
        output += "{:<12s}  {:>10.2f}   {:>10.2f}\n".format(p, fit.params[p],
                                                            fit.bse[p])
    
    output += "---\n"
    output += "n = {}, k = {}\n".format(int(fit.nobs), int(fit.df_model)+1)
    
    # residual sd from Pg 41
    resid_sd = np.sqrt(np.sum(fit.resid**2)/(fit.nobs-fit.df_model-1))
    output += "residual sd = {:.2f}, R-squared = {:.2f}\n".format(resid_sd,
                                                                  fit.rsquared)
    
    print(output)
```

Now, use it:

```python
display(fit)
```

## Print, Pg 39

Too little information?  Just the parameters.

```python
print(fit.params)
```

## Summary, Pg 38

Too much information? A ton of (undesireable?) information.

```python
print(fit.summary())
```
