# Installation

**kima** is written in C++ and needs to be compiled, so you will need a working
C++ compiler.

We run regular tests with the *gcc* and *clang* compilers: 

<img style='vertical-align:middle;' src='https://github.com/j-faria/kima/actions/workflows/compilers.yml/badge.svg'>


To install, first clone the GitHub repository :octocat:
```sh
git clone --recursive https://github.com/j-faria/kima.git  #(1)
```

1.  don't foget the `--recursive` to download the required submodules.


then go to the *kima* directory and compile the code (in parallel)

```sh
cd kima
make -j 4
```

To analyse the results, **kima** comes with a helper Python package called
`pykima`.  
To install `pykima`, from inside the *kima* directory run

```sh
python setup.py install  # or python setup.py develop
```


!!! note  
    You may choose to analyse the results without `pykima` since the inputs and
    outputs of **kima** itself are simple text files. `pykima` will just make it
    a bit easier.
 
 
 If the installation fails, take a look at the
 [troubleshooting](https://github.com/j-faria/kima/wiki/Troubleshooting){:target="_blank"}
 section of the wiki or [open an
 issue](https://github.com/j-faria/kima/issues){:target="_blank"}.