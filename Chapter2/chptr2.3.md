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

