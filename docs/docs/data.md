# Data

**kima** can load different kinds of data, either from column-based text files
or Python arrays. The different data classes have some useful attributes and
methods.

## Radial velocity time series (`RVData`)

The simplest dataset is a time series of radial velocity measurements from one
single instrument. The `RVData` class is used to load these data from a file,
which must contain _at least_ three columns:

```title="data.txt"
time   RV   RVerror
...    ...  ...
```

and can be loaded as 

```python
from kima import RVData

data = RVData('data.txt', skip=1)
```

The columns in the file can be space (` ␣ `), TAB (` ⇥ `), or comma (` , `) separated.

!!! note
    The file can have more columns; by default, **kima** will
    read the first three and ignore any remaining ones.  
    Aso, you can actually mix separators in the same file! But please don't... 
    :face_with_raised_eyebrow:  

Regarding units, the first column is assumed to be in days (although this is not
strictly enforced), and the RVs and uncertainties are by default assumed to be
given in m/s. Most importantly, they both have to be in the same units!

The `skip` argument can be used to skip the first line(s) of the file.

If you want to load RV data from multiple instruments, simply provide a list of 
filenames:

=== "instrument 1"

    ```title="data_inst1.txt"
    time   RV   RVerror
    ...    ...  ...
    ```

=== "instrument 2"

    ```title="data_inst2.txt"
    time   RV   RVerror
    ...    ...  ...
    ```


```python
data = RVData(['data_inst1.txt', 'data_inst2.txt'], skip=1)
```

### Useful methods

A few methods of the `RVData` class can be useful, e.g., when setting priors:

```python
data.get_timespan()
data.get_RV_span()
```

The full documentation is available [here][kima.Data.RVData].


### Quick plot

The `RVData` class has a `plot` method that allows you to quickly check the
data:

```python
data.plot() #(1)!
```

1. This plot show the RVs from the [51 Peg example](/docs/examples/51Peg).

![alt text](image.png)


## Activity indicators

Sometimes it's useful to use activity indicators simultaneous to the RVs. These
can be considered as basis vectors for decorrelation or for joint GP fits, for
example. The `RVData` class can also load these data if they are stored in the
subsequent columns of a file:

```title="data_with_indicators.txt"
time   RV   RVerror   ind1    ind2
...    ...  ...       ...     ...
```

which can be loaded as 

```python
data = RVData('data.txt', skip=1, indicators=['ind1', 'ind2'])
```

The data is stored in the `actind` (read-only) attribute of the `RVData` object,
which appears as a list of lists. 

!!! note
    A particular column of the file can easily be skipped by providing an empty
    string as the indicator name:

    ```python
    data = RVData('data.txt', skip=1, indicators=['', 'ind2'])
    ```

Provided the indicators were loaded, the
[RVmodel.indicator_correlations][kima.RVmodel.RVmodel.indicator_correlations]
option can be used to model linear correlations. On the other hand, some models,
like the [RVFWHMmodel][kima.RVFWHMmodel] for example, assume that the provided
`RVData` object has two activity indicators corresponding to the FWHM and its
uncertainty.


## Hipparcos-Gaia proper motions (`HGPMdata`)


!!! warning "TODO"
    