---
hide:
  - toc
---

# API documentation for `kima`

### Data

Most analyses will start by using the [`RVData`][kima.Data.RVData] class to load data
from a set of files or arrays. If using Gaia epoch astrometry the the [`GAIAdata`][kima.Data.GAIAdata] class
is used to load that in.

#### Example:

```py
from kima import RVData

data = RVData('filename.txt', skip=1) #(1)!

data.plot() #(2)!
```

1. With `skip=1`, the first line of the file will be ignored.
2. A very simple plot of the dataset.


??? example "`kima.RVData` API"

    ::: kima.Data.RVData
        handler: python
        options:
          filters:
            - "!__"
            - "__init__"
            
??? example "`kima.GAIAdata` API"

    ::: kima.Data.GAIAdata
        handler: python
        options:
          filters:
            - "!__"
            - "__init__"


### Models

We can then create one of the several models currently implemented

#### Example:
```py
from kima import RVmodel

model = RVmodel(fix=False, npmax=2, data=data)
```

??? tip "`kima.RVmodel` API"

    ::: kima.RVmodel
        handler: python
        options:
          filters:
            - "!KeplerianConditionalPrior"
            - "!ApodizedKeplerianConditionalPrior"
            - "!TRANSITConditionalPrior"
            - "!__"
            - "__init__"


??? tip "`kima.GPmodel` API"

    ::: kima.GPmodel
        options:
          filters:
            - "!__"


??? tip "`kima.RVFWHMmodel` API"

    ::: kima.RVFWHMmodel
        options:
          filters:
            - "!__"


??? tip "`kima.BINARIESmodel` API"

    ::: kima.BINARIESmodel
        options:
          filters:
            - "!__"
            
??? warning "TODO: `kima.RVHGPMmodel` API"

    <!-- ::: kima.RVHGPMmodel
        options:
          filters:
            - "!RVHGPMConditionalPrior"
            - "!__" -->

??? tip "`kima.GAIAmodel` API"

    ::: kima.GAIAmodel
        options:
          filters:
            - "!GAIAConditionalPrior"
            - "!__"

??? tip "`kima.RVGAIAmodel` API"

    ::: kima.RVGAIAmodel
        options:
          filters:
            - "!RVGAIAConditionalPrior"
            - "!__"



The `model.conditional` object contained in each model can be used to define the
priors for the orbital parameters. Different models may use different
conditionals, depending on the specific parameterisation:

??? tip "`RVmodel` / `GPmodel` / `RVFWHMmodel` → `KeplerianConditionalPrior`"

    ::: kima.RVmodel.KeplerianConditionalPrior
        options:
          filters:
            - "!__"

??? tip "`GAIAmodel` → `GAIAConditionalPrior`"

    ::: kima.GAIAmodel.GAIAConditionalPrior
        options:
          filters:
            - "!__"


??? warning "TODO: `RVHGPMmodel` → `RVHGPMConditionalPrior`"

    <!-- ::: kima.RVHGPMmodel.RVHGPMConditionalPrior
        options:
          filters:
            - "!__" -->


### Run

To run the sampler and estimate the posterior for the model parameters 
just call [`kima.run`][kima.Sampler.run]

#### Example:
```py
import kima

kima.run(model, steps=1000, num_threads=4)
```

??? danger "`kima.run` API"

    ::: kima.Sampler.run
        handler: python
        options:
          show_root_heading: false
          show_signature: false
          separate_signature: false
          heading_level: 2
          filters:
            - "!__"

--- 

### Distributions

The `distributions` sub-package contains the implementation of several probability distributions 
that can be used as priors for a model parameter. All distributions have standard parameterizations, 
as you would find e.g. in Wikipedia. Some distributions have a corresponding 
`Truncated` version which truncates the support of the distribution to an
interval $ \[{\rm lower}, {\rm upper} \]$.

#### Example:
```py
from kima import distributions

parameter_prior = distributions.Gaussian(10, 1)
```

<!-- ::: kima.distributions
    handler: python
    options:
      show_root_heading: false
      heading_level: 6
      show_if_no_docstring: true
      show_bases: false
      show_docstring_description: false
      filters:
        - "!DiscreteDistribution"
        - "!Distribution"
        - "!RNG"
        - "!logpdf"
        - "!cdf"
        - "!ppf"
        - "!loc"
        - "!scale"
        - "!alpha"
        - "!beta"
        - "!lower"
        - "!upper"
        - "!__" -->


??? example "`kima.distributions` API"

    ::: kima.distributions
        handler: python
        options:
          show_root_heading: false
          show_bases: false
          filters:
            - "!__"
            - "!Distribution"

### Keplerian

Finally, even though it sits at the core of what **kima** does, 
there is the [keplerian][kima.kepler.keplerian] function:


??? example "`kima.keplerian` API"
    
    ::: kima.kepler.keplerian
    
This is the default radial velocity keplerian function, though others exist for the more specific cases.
There is the keplerian function for Gaia astrometry:

??? example "`kima.keplerian_gaia` API"
    
    ::: kima.kepler.keplerian_gaia
    
<!-- 
And for the [BINARIESmodel][kima.BINARIESmodel] there is a special keplerian that includes 
post-Keplerian corrections and apsidal precession:

??? example "`kima.post_keplerian` API"
 -->
    
<!--     ::: kima.postkepler.post_keplerian -->