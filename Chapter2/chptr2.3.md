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

The **polls.dat** file has an unusual format. The data that we'd like to have in
a single row is split across 4 rows:

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

polls = pd.DataFrame(data, columns=[u'year', u'month', u'perc for', u'perc against', u'perc no opinion'])
polls.head()
```

```python
#R: support <- polls[,3]/(polls[,3]+polls[,4])
#
# --Note: this give the (percent) support for thise that have an opinion
# --The percentage with no opinion are ignored
# --This results in difference between our plot (below) and the Gallup plot (link above)
polls[u'support'] = polls[u'perc for']/(polls[u'perc for']+polls[u'perc against'])
polls.head()
```

```python
#R: year <-  polls[,1] + (polls[,2]-6)/12
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
plt.ylabel(u'Percentage support for the death penalty')
plt.xlabel(u'Year')
# you can adjust y-limits with command like below
# I will leave the default behavior
#plt.ylim(np.min(100*polls[u'support'])-2, np.max(100*polls[u'support']+2))

fig = plt.gcf()
fig.set_size_inches(6,6)
```

## Weighted averages, Pg 19

The example R-code for this part is incomplete, so I will make up `N`, `p` and
`se` loosely related to the text on page 19.

```python
#R: N <- c(66030000, 81083600, 60788845)
#   p <- c(0.55, 0.61, 0.38)
#   se <- c(0.02, 0.03, 0.03)
N = np.array([66030000, 81083600, 60788845])
p = np.array([0.55, 0.61, 0.38])
se = np.array([0.02, 0.03, 0.03])
```

```python
#R: w.avg <- sum(N*p)/sum(N)
w_avg = np.sum(N*p)/np.sum(N)
w_avg
```

```python
#R: se.w.avg <- sqrt(sum((N*se/sum(N))^2))
se_w_avg = np.sqrt(np.sum((N*se/np.sum(N))**2))
se_w_avg
```

```python
# this uses +/- 2 std devs
#R: int.95 <- w.avg + c(-2,2)*se.w.avg
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
#R: n.men <- 500
n_men = 500
n_men
```

```python
#R: p.hat.men <- 0.75
p_hat_men = 0.75
p_hat_men
```

```python
#R: se.men <- sqrt (p.hat.men*(1-p.hat.men)/n.men)
se_men = np.sqrt(p_hat_men*(1.-p_hat_men)/n_men)
se_men
```

```python
#R: n.women <- 500
n_women = 500
n_women
```

```python
#R: p.hat.women <- 0.65
p_hat_women = 0.65
p_hat_women
```

```python
#R: se.women <- sqrt(p.hat.women*(1-p.hat.women)/n.women)
se_women = np.sqrt(p_hat_women*(1.-p_hat_women)/n_women)
se_women
```

```python
#R: n.sims <- 10000
n_sims = 10000
n_sims
```

```python
#R: p.men <- rnorm(n.sims, p.hat.men, se.men)
p_men = norm.rvs(size=n_sims, loc=p_hat_men, scale=se_men)
p_men[:10] # show first ten
```

```python
#R: p.women <- rnorm(n.sims, p.hat.women, se.women)
p_women = norm.rvs(size=n_sims, loc=p_hat_women, scale=se_women)
p_women[:10] # show first ten
```

```python
#R: ratio <- p.men/p.women
ratio = p_men/p_women
ratio[:10] # show first ten
```

```python
#R: int.95 <- quantile(ratio, c(.025,.975))

# the values of alphap and betap replicate the R default behavior
# see http://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.mstats.mquantiles.html
int95 = mquantiles(ratio, prob=[0.025,0.975], alphap=1., betap=1.)
int95
```

