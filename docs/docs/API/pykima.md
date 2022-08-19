# API documentation for `pykima`


The `pykima.results` module contains the `KimaResults` class for storing,
analysing, and plotting the results from a **kima** run.
Users will typically *not* instantiate the class directly, but instead use the
high-level `load` function

```python
>>> res = pk.load()
>>> res
KimaResults(lnZ=..., ESS=...)
```

??? example "`pykima.results` API"

    ::: pykima.results
        handler: python
        options:
          show_root_heading: false
          show_root_toc_entry: false
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
          show_root_toc_entry: false
          heading_level: 2


---

The `pykima.utils` module contains a few less commonly used utility functions:

??? example "`pykima.utils` API"

    ::: pykima.utils
        handler: python
        options:
          show_root_heading: true
          show_root_toc_entry: false
          heading_level: 2
