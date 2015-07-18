# ARM-ipynb

Running through the examples in Gelman and Hill's *Data Analysis Using
Regression and Multilevel/Hierarchical Models* in both R and Python using
IPython/Jupyter notebooks. To make the most of thi code you need a copy of the
book-- buy one or get from the library.  The websites for the book and
data/code are:

* [book website](http://www.stat.columbia.edu/~gelman/arm/)

* [book software and data files](http://www.stat.columbia.edu/~gelman/arm/software/)

If you'd like to use the IPython notebooks, just grab them and compute away. Of
course, relevant Python and R packages will need to be installed for the 
notebooks to work on you local machine.

If you'd like to start with the R-markdown (.Rmd) and markdown (.md) files,
those are available as well. Notes on how to turn these into the
IPython notebooks (.ipynb) are given below. These use the Python package
[notedown](https://github.com/aaren/notedown) to do the conversions.

## Notes

To convert from **Rmd** and **md** to **ipynb** and back use the following
patterns:

### File Conversion

**Python: md to ipynb**

```bash
notedown chptr2.3.md > chptr2.3.ipynb
```

**R: Rmd to ipynb**

I add the `-R.ipynb` ending for notebooks running R code using `rpy2`.

```bash
notedown chptr2.3.Rmd --knit --rmagic > chptr2.3-R.ipynb
```
