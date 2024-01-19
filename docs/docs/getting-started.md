
To get started, read in a dataset and choose one of the available [models](/docs/models):

```py
import kima
from kima import RVData, RVmodel

data = RVData('my_dataset')

model = RVmodel(fix=True, npmax=1, data=data)
```

and then run the model

```py
kima.run(model, steps=1000)
```

The resulting posteriors can be loaded and analysed easily (more information [here](/docs/API/pykima))

```py
res = kima.load_results()
```


## Running the examples

**kima** comes packed with ready-to-run examples. 
All of them are described to some length [here](/docs/examples).


### Finding 51 Peg b

One of the examples involves the detection of 51 Peg _b_ in Keck/HIRES data.
Running it is as simple as 

```py
from kima.examples import _51Peg

model = _51Peg(run=True)
```

and the results are loaded with

```py
res = kima.load_results()

res.plot1() #(1)
res.plot2() #(2)
```

1.  shows the posterior for the number of planets \(N_p\)
2.  shows the posterior for the orbital period, with a clear peak at 4.23 days


<!-- 
## Creating a template directory

You can use the `kima-template` script to create a template directory containing
the necessary files to run **kima**

```sh
kima-template my-new-planet
# Populating directory my-new-planet
# with kima template files: kima_setup.cpp, OPTIONS, Makefile, README.md
```

You can now modify the `kima_setup.cpp` file to set up the model and run the
analysis with `kima-run` as for the examples.
 -->

