# API documentation for `pykima`

**kima** itself is written in C++, to leverage the increased performance. The
Python bindings created with [nanobind](https://github.com/wjakob/nanobind)
expose most of the functionality of the data ([RVData][kima.Data.RVData]) and
model classes (e.g. [RVmodel][kima.RVmodel], [GPmodel][kima.GPmodel], etc.), as
well as the sampler ([kima.run][kima.Sampler.run]).

To analyse the results and create figures, the `pykima` sub-package was created.
It can be used in an IPython shell, a Jupyter notebook, or the standard Python
interpreter.

---

The typical interface with the results of a run is through the
[`KimaResults`][pykima.results.KimaResults] class created by calling the
[`kima.load_results`][pykima.results.load_results] function

```python
model ...
res = kima.load_results(model)
```


??? example "`KimaResults` class"

    ::: kima.pykima.results.KimaResults
        handler: python
        options:
          show_source: false
          filters:
            - "!^_"
            - "!ESS"

??? example "`kima.load_results` API"

    ::: kima.pykima.results.load_results
        handler: python
        options:
          show_source: false
          filters:
            - "!^_"


A number of convenience plots are redirected from the
[`pykima.display`][kima.pykima.display] module into any
[`KimaResults`][kima.pykima.results.KimaResults] instance:


```python
res.plot_posterior_np()

res.plot_posterior_period()
```

!!! note

    Several of the plotting functions are aliased and numbered, as in

    ```py
    res.plot1 = res.plot_posterior_np
    ```

    This can be convenient, but it's mostly for historical reasons and 
    the API might change at any time. Therefore, these funcions are not
    documented.



??? example "`kima.pykima.analysis` API"

    ::: kima.pykima.analysis
        handler: python
        options:
          show_source: false
          filters:
            - "!^_"


??? example "`kima.pykima.display` API"

    ::: kima.pykima.display
        handler: python
        options:
          show_source: false
          filters:
            - "!^_"

??? example "`kima.pykima.utils` API"

    ::: kima.pykima.utils
        handler: python
        options:
          members_order: "alphabetical"
          filters:
            - "!^_"
