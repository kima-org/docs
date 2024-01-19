# Installation

**kima** can easily be installed using pip:

```sh
pip install kima
```

[![PyPI version](https://badge.fury.io/py/kima.svg)](https://badge.fury.io/py/kima)

Installation is tested on Linux, Windows, and MacOS regularly.
If the following badge is green, things should be working 
(even if it's red, go ahead and try it anyway :shrug:)

[![test pip install](https://github.com/kima-org/test-pip-install/actions/workflows/python-package.yml/badge.svg)](https://github.com/kima-org/test-pip-install/actions/workflows/python-package.yml)

!!! danger
    The installation process compiles the C++ code using [CMake](https://cmake.org/),
    and requires at least CMake v3.17, which should be easy to install (or update)
    on most platforms. See [this official page](https://cmake.org/download/) for more information.


Alternatively, you can also clone the [:material-github: GitHub repository](https://github.com/kima-org/kima.git)

```sh
git clone https://github.com/kima-org/kima.git
```

and install the package locally

```sh
cd kima
pip install -e .
```

 
If the installation fails,
open an issue [here](https://github.com/kima-org/kima/issues){:target="_blank"}.
<!-- take a look at the [troubleshooting](/docs/troubleshooting) page -->