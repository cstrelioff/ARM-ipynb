# 2.3: Classical confidence intervals

```python
from __future__ import print_function, division
%matplotlib inline

import matplotlib
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
# use matplotlib style sheet
plt.style.use('ggplot')
```

## CI for continuous data, Pg 18

```python
# import the t-distribution from scipy.stats
from scipy.stats import t
```

```python
y = np.array([35,34,38,35,37])
y
```

```python
n = len(y)
n
```

```python
estimate = np.mean(y)
estimate
```

Numpy uses a denominator of **N** in the standard deviation calculation by
default, instead of **N-1**. To use **N-1**, the unbiased estimator-- and to
agree with the R output, we have to give `np.std()` the argument `ddof=1`:

```python
se = np.std(y, ddof=1)/np.sqrt(n)
se
```

```python
int50 = estimate + t.ppf([0.25, 0.75], n-1)*se
int50
```

```python
int95 = estimate + t.ppf([0.025, 0.975], n-1)*se
int95
```

## CI for proportions, Pg 18

```python
from scipy.stats import norm
```

```python
y = 700
y
```

```python
n = 1000
n
```

```python
estimate = y/n
estimate
```

```python
se = np.sqrt(estimate*(1-estimate)/n)
se
```

```python
int95 = estimate + norm.ppf([.025,0.975])*se
int95
```

## CI for discrete data, Pg 18

```python
y = np.repeat([0,1,2,3,4], [600,300, 50, 30, 20])
y
```

```python
n = len(y)
n
```

```python
estimate = np.mean(y)
estimate
```

See the note above about the difference different defaults for standard
deviation in Python and R.

```python
se = np.std(y, ddof=1)/np.sqrt(n)
se
```

```python
int50 = estimate + t.ppf([0.25, 0.75], n-1)*se
int50
```

```python
int95 = estimate + t.ppf([0.025, 0.975], n-1)*se
int95
```

## Plot Figure 2.3, Pg 19

The **polls.dat** file has an unusual format. The data that we would like to
have in a single row is split across 4 rows:

* year month
* percentage support
* percentage against
* percentage no opinion

The data seems to be a subset of the Gallup data, available here:
http://www.gallup.com/poll/1606/Death-Penalty.aspx

We can see the unusual layout using the **bash** command *head* (linux/osx only,
sorry..)

```python
%%bash
head ../../ARM_Data/death.polls/polls.dat
```

Using knowledge of the file layout we can read in the file and pre-process into
appropriate rows/columns for passing into a pandas dataframe:

```python
# Data is available in death.polls directory of ARM_Data
data = []
temp = []
ncols = 5
with open("../../ARM_Data/death.polls/polls.dat") as f:
    for line in f.readlines():
        for d in line.strip().split(' '):
            temp.append(float(d))
        if (len(temp) == ncols):
            data.append(temp)
            temp = []

polls = pd.DataFrame(data, columns=[u'year', u'month', u'perc for', 
                     u'perc against', u'perc no opinion'])
polls.head()
```

```python
# --Note: this give the (percent) support for thise that have an opinion
# --The percentage with no opinion are ignored
# --This results in difference between our plot (below) and the Gallup plot (link above)
polls[u'support'] = polls[u'perc for']/(polls[u'perc for']+polls[u'perc against'])
polls.head()
```

```python
polls[u'year_float'] = polls[u'year'] + (polls[u'month']-6)/12
polls.head()
```

```python
# add error column -- symmetric so only add one column
# assumes sample size N=1000
# uses +/- 1 standard error, resulting in 68% confidence
polls[u'support_error'] = np.sqrt(polls[u'support']*(1-polls[u'support'])/1000)
polls.head()
```

```python
fig, ax = plt.subplots(figsize=(8, 6))
plt.errorbar(polls[u'year_float'], 100*polls[u'support'],
             yerr=100*polls[u'support_error'], fmt='ko',
             ms=4, capsize=0)
plt.ylabel(u'Percentage support for the death penalty')
plt.xlabel(u'Year')

# you can adjust y-limits with command like below
# I will leave the default behavior
#plt.ylim(np.min(100*polls[u'support'])-2, np.max(100*polls[u'support']+2))
```

## Weighted averages, Pg 19

The example R-code for this part is incomplete, so I will make up *N*, *p* and
*se* loosely related to the text on page 19.

```python
N = np.array([66030000, 81083600, 60788845])
p = np.array([0.55, 0.61, 0.38])
se = np.array([0.02, 0.03, 0.03])
```

```python
w_avg = np.sum(N*p)/np.sum(N)
w_avg
```

```python
se_w_avg = np.sqrt(np.sum((N*se/np.sum(N))**2))
se_w_avg
```

```python
# this uses +/- 2 std devs
int_95 = w_avg + np.array([-2,2])*se_w_avg
int_95
```

## CI using simulations, Pg 20

```python
# import the normal from scipy.stats
# repeated to make sure that it is clear that it is needed for this section
from scipy.stats import norm

# also need this for estimating CI from samples
from scipy.stats.mstats import mquantiles
```

```python
n_men = 500
n_men
```

```python
p_hat_men = 0.75
p_hat_men
```

```python
se_men = np.sqrt(p_hat_men*(1.-p_hat_men)/n_men)
se_men
```

```python
n_women = 500
n_women
```

```python
p_hat_women = 0.65
p_hat_women
```

```python
se_women = np.sqrt(p_hat_women*(1.-p_hat_women)/n_women)
se_women
```

```python
n_sims = 10000
n_sims
```

```python
p_men = norm.rvs(size=n_sims, loc=p_hat_men, scale=se_men)
p_men[:10] # show first ten
```

```python
p_women = norm.rvs(size=n_sims, loc=p_hat_women, scale=se_women)
p_women[:10] # show first ten
```

```python
ratio = p_men/p_women
ratio[:10] # show first ten
```

```python
# the values of alphap and betap replicate the R default behavior
# see http://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.mstats.mquantiles.html
int95 = mquantiles(ratio, prob=[0.025,0.975], alphap=1., betap=1.)
int95
```

