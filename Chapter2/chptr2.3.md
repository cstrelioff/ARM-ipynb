# 2.3: Classical confidence intervals

```python
from __future__ import print_function, division
%matplotlib inline

import matplotlib
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

```

## CI for continuous data, Pg 18

```python
# import the t-distribution from scipy.stats
from scipy.stats import t
```

```python
#R: y <- c(35,34,38,35,37)
y = np.array([35,34,38,35,37])
y
```

```python
#R: n <- length(y)
n = len(y)
n
```

```python
#R: estimate <- mean(y)
estimate = np.mean(y)
estimate
```

Numpy uses a denominator of **N** in the standard deviation calculation by
default, instead of **N-1**. To use **N-1**, the unbiased estimator-- and to
agree with the R output, we have to give `np.std()` the argument `ddof=1`:

```python
#R: se <- sd(y)/sqrt(n)
se = np.std(y, ddof=1)/np.sqrt(n)
se
```

```python
#R: int.50 <- estimate + qt(c(.25,.75),n-1)*se
int50 = estimate + t.ppf([0.25, 0.75], n-1)*se
int50
```

```python
#R: int.95 <- estimate + qt(c(.025,.975),n-1)*se
int95 = estimate + t.ppf([0.025, 0.975], n-1)*se
int95
```

## CI for proportions, Pg 18

```python
# import the normal from scipy.stats
from scipy.stats import norm
```

```python
#R: y <- 700
y = 700
y
```

```python
#R: n <- 1000
n = 1000
n
```

```python
#R: estimate <- y/n
estimate = y/n
estimate
```

```python
#R: se <- sqrt (estimate*(1-estimate)/n)
se = np.sqrt(estimate*(1-estimate)/n)
se
```

```python
#R: int.95 <- estimate + qnorm(c(.025,.975))*se
int95 = estimate + norm.ppf([.025,0.975])*se
int95
```

## CI for discrete data, Pg 18

```python
#R: y <- rep(c(0,1,2,3,4), c(600,300,50,30,20))
y = np.repeat([0,1,2,3,4], [600,300, 50, 30, 20])
y
```

```python
#R: n <- length(y)
n = len(y)
n
```

```python
#R: estimate <- mean(y)
estimate = np.mean(y)
estimate
```

See the note above about the difference different defaults for standard
deviation in Python and R.

```python
#R: se <- sd(y)/sqrt(n)
se = np.std(y, ddof=1)/np.sqrt(n)
se
```

```python
#R: int.50 <- estimate + qt(c(.25,.75),n-1)*se
int50 = estimate + t.ppf([0.25, 0.75], n-1)*se
int50
```

```python
#R: int.95 <- estimate + qt(c(.025,.975),n-1)*se
int95 = estimate + t.ppf([0.025, 0.975], n-1)*se
int95
```

## Plot Figure 2.3, Pg 19

```python
#R: par(mar=c(5,5,4,2)+.1)
```

The **polls.dat** file has an unsual format--

```python
%%bash
head ../../ARM_Data/death.polls/polls.dat
```

```python
# Data is available in death.polls directory of ARM_Data
# R: polls <- matrix(scan("../../ARM_Data/death.polls/polls.dat"),
#                    ncol=5, byrow=TRUE)
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

polls = pd.DataFrame(data, columns=[u'year', u'month', u'for', u'against', u'unknown'])
polls.head()
```

```python
#R: support <- polls[,3]/(polls[,3]+polls[,4])
polls[u'support'] = polls[u'for']/(polls[u'for']+polls[u'against'])
polls.head()
```

```python
#R: year <-  polls[,1] + (polls[,2]-6)/12
polls[u'year_float'] = polls[u'year'] + (polls[u'month']-6)/12
polls.head()
```

```python
# add error column -- symmetric so only add one column
# assumes sample size -- why?? Isn't it 100??
polls[u'support_error'] = np.sqrt(polls[u'support']*(1-polls[u'support'])/1000)
polls.head()
```

```python
#R: plot(year, support*100, xlab="Year", ylim=c(min(100*support)-1, max(100*support)+1),
#        ylab="Percentage support for the death penalty", cex=1.1, cex.main=1.2,
#        cex.axis=1.1, cex.lab=1.1, pch=20)
#   
#   for (i in 1:nrow(polls))
#     lines (rep(year[i],2), 100*(support[i]+c(-1,1)*sqrt(support[i]*(1-support[i])/1000)))
plt.style.use('ggplot')
plt.errorbar(polls[u'year_float'], 100*polls[u'support'],
             yerr=100*polls[u'support_error'], fmt='ko',
             ms=4, capsize=0)
plt.ylim(np.min(100*polls[u'support'])-2, np.max(100*polls[u'support']+2))
plt.ylabel(u'Percentage support for the death penalty')
plt.xlabel(u'Year')

fig = plt.gcf()
fig.set_size_inches(6,6)
```

```python

```
