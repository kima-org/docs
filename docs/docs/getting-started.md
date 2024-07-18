---
comments: true
---

## Data, models, and results

To get started, read in a dataset and choose one of the available [models](/docs/models):

```python
import kima
from kima import RVData, RVmodel

data = RVData('my_dataset')

model = RVmodel(fix=True, npmax=1, data=data)
```

and then run the model

```python
kima.run(model, steps=1000)
```

The resulting posteriors can be loaded and analysed easily (more information [here](/docs/API/pykima))

```python
res = kima.load_results(model)
res.plot_posterior_periods()
```


## Running the examples

**kima** comes packed with ready-to-run examples. 
All of them are described to some length [here](/docs/examples).


### Finding 51 Peg b

One of the examples involves the detection of 51 Peg _b_ in Keck/HIRES data.
Running it is as simple as 

```py
from kima.examples import _51Peg

model, res = _51Peg(run=True, load=True) #(1)
```

1. This will run **kima** for 5000 steps, taking a few seconds.

and the results object provides a number of useful plots 

```py
res.plot_posterior_np() #(1)

res.plot_posterior_periods() #(2)

p = res.maximum_likelihood_sample() #(3)
res.phase_plot(p) #(4)
```

1. shows the posterior for the number of planets
2. shows the posterior for the orbital period, with a clear peak at 4.23 days
3. select the sample with the maximum likelihood value
4. plot the maximum likelihood solution in phase


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

