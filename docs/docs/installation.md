---
comments: true
---

# Installation

### From PyPI

**kima** can easily be installed using pip:

```sh
pip install kima
```

[![PyPI version](https://badge.fury.io/py/kima.svg)](https://badge.fury.io/py/kima)

Installation is tested on Linux, Windows, and MacOS regularly.
If the following badge is green, things should be working 
(even if it's red, go ahead and try it anyway :shrug:)

[![test pip install](https://github.com/kima-org/test-pip-install/actions/workflows/python-package.yml/badge.svg)](https://github.com/kima-org/test-pip-install/actions/workflows/python-package.yml){:target="_blank"}


!!! danger
    In some cases, the installation process will compile the C++ code using
    [CMake](https://cmake.org/), which requires at least CMake v3.17. It should
    be easy to install (or update) on most platforms. See [this official
    page](https://cmake.org/download/) for more information.


### From the repository

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
open an issue [here](https://github.com/kima-org/kima/issues){:target="_blank"}
or comment below.
<!-- take a look at the [troubleshooting](/docs/troubleshooting) page -->