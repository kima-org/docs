# API documentation for `pykima`

**kima** is written in C++, to leverage the increased performance. But a more
dynamic language like Python is better suited to analyse the results and create
figures. The `pykima` package was created to provide a bridge between the
two languages. It can be used in an IPython shell, a Jupyter notebook, or the
standard Python interpreter. Simply type

```py
import pykima as pk
```

---

The `pykima.results` module contains the `KimaResults` class for storing,
analysing, and plotting the results from a **kima** run.
Users will typically *not* instantiate the class directly, but instead use the
high-level `load` function

```py
>>> res = pk.load()
>>> res
KimaResults(lnZ=..., ESS=...)
```

This class has a number of useful attributes and methods, described below.


??? example "`pykima.results` API"

    ::: pykima.results
        handler: python
        options:
          show_root_heading: false
          show_root_toc_entry: true
          heading_level: 2


---

A number of functions for more complicated analysis of the results are
implemented in the `pykima.analysis` module. Most of these functions take a
`KimaResults` instance as argument.

??? example "`pykima.analysis` API"

    ::: pykima.analysis
        handler: python
        options:
          show_root_heading: false
          show_root_toc_entry: true
          heading_level: 2


---

The `pykima.utils` module contains a few less commonly used utility functions:

??? example "`pykima.utils` API"

    ::: pykima.utils
        handler: python
        options:
          show_root_heading: false
          show_root_toc_entry: true
          heading_level: 2
