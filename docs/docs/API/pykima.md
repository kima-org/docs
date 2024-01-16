# API documentation for `pykima`

**kima** itself is written in C++, to leverage the increased performance. 
The Python bindings created with [nanobind](https://github.com/wjakob/nanobind)
expose most of the functionality of the data ([RVData][kima.RVData]) 
and model classes (e.g. [RVmodel][kima.RVmodel], [GPmodel][kima.GPmodel], etc.),
as well as the sampler ([kima.run][kima.run]).

To analyse the results and create figures, the `pykima` sub-package was created.
It can be used in an IPython shell, a Jupyter notebook, or the standard Python interpreter.

---

