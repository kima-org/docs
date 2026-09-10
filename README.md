<p align="center">
<img src="https://www.kima.science/assets/logo_transparent.png" 
     width="150" alt="Logo created by Solène Ulmer-Moll">

<p align="center">
    Visit the documentation at 
    <a href="https://www.kima.science/docs">kima.science</a>
</p>
</p>


### Local development

The documentation is built using
[Zensical](https://zensical.org/docs/get-started/):

```bash
$ # pip install zensical

$ zensical serve
Serving .../kima-docs/site on http://localhost:8000
```

### Examples

Each individual example can be found in the `docs/docs/examples` folder. Most
examples are [Marimo](https://marimo.io/) notebooks, which can be edited with

```bash
$ marimo edit [example_name].py
```

These marimo notebooks are [automatically exported to Jupyter
notebooks](https://docs.marimo.io/guides/exporting/jupyter_notebook/), ending up
in the `docs/docs/examples/__marimo__` folder. Then, the Jupyter notebooks are
converted to Markdown using the `make notebooks` command. 

```make "Makefile"
.PHONY: notebooks

NOTEBOOK_PATH := docs/docs/examples/__marimo__
NOTEBOOK_OUT_DIR := --output-dir=docs/docs/examples/
JUPYTER = --from jupyter-core jupyter

notebooks:
	uvx $(JUPYTER) nbconvert.exe --to markdown $(NOTEBOOK_PATH)/51Peg.ipynb $(NOTEBOOK_OUT_DIR)
	...
```

```bash
$ make notebooks
```


Native Jupyter notebooks can also be added and converted in the same way.

!!! note

    The output from a **kima** run may not display correctly in a Jupyter notebook, so using a native Jupyter notebook for the examples is not recommended.

