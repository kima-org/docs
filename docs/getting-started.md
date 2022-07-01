## Running the examples

**kima** comes packed with ready-to-run examples inside the *kima/examples*
directory. All of them are described to some length
[here]({{site.baseurl}}/examples).

To run any example, go to the directory and use the `kima-run` script (part of
`pykima`)

```bash
cd examples/*CHOOSE ONE*
kima-run
```

## Finding 51 Peg b




## Creating a template directory

After installing `pykima`, you can use the `kima-template` script to create a
template directory containing the necessary files to run **kima**

```sh
kima-template my-new-planet
# Populating directory my-new-planet
# with kima template files: kima_setup.cpp, OPTIONS, Makefile, README.md
```

You can now modify the `kima_setup.cpp` file to set up the model and run the
analysis with `kima-run` as for the examples.

