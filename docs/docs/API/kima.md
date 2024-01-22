---
hide:
  - toc
---

# kima API

Most analyses will use the [`RVData`][kima.RVData] class to load RV data
from a set of files or arrays

#### Example:
```py
from kima import RVData

data = RVData('filename.txt', skip=1)
```

??? example "`kima.RVData` API"

    ::: kima.RVData
        handler: python
        options:
          docstring_style: "sphinx"
          show_signature_annotations: true
          signature_crossrefs: true
          heading_level: 6
          filters:
            - "!__"


and then define one of the several models currently implemented

#### Example:
```py
from kima import RVmodel

model = RVmodel(fix=False, npmax=2, data=data)
```

???+ tip "`kima.RVmodel` API"

    ::: kima.RVmodel
        handler: python
        options:
          docstring_style: "google"
          heading_level: 5
          merge_init_into_class: true
          filters:
            - "!RVConditionalPrior"
            - "!TRANSITConditionalPrior"
            - "!__"
            - "__init__"


??? tip "`kima.GPmodel` API"

    ::: kima.GPmodel
        handler: python
        options:
          heading_level: 5
          filters:
            - "!RVConditionalPrior"
            - "!TRANSITConditionalPrior"
            - "!__"


??? tip "`kima.RVFWHMmodel` API"

    ::: kima.RVFWHMmodel
        handler: python
        options:
          heading_level: 5
          filters:
            - "!RVConditionalPrior"
            - "!TRANSITConditionalPrior"
            - "!__"


??? tip "`kima.BINARIESmodel` API"

    ::: kima.BINARIESmodel
        handler: python
        options:
          heading_level: 5
          filters:
            - "!RVConditionalPrior"
            - "!TRANSITConditionalPrior"
            - "!__"



To run the sampler and estimate the posterior for the model parameters just call [`kima.run`][kima.run]

#### Example:
```py
import kima

kima.run(model, steps=1000, num_threads=4)
```

??? danger "`kima.run` API"
    ::: kima.run
        handler: python
        options:
          show_root_heading: false
          heading_level: 2
          filters:
            - "!__"



--- 

The `distributions` sub-package contains the implementation of several probability distributions 
that can be used as priors for a model parameter. All distributions have standard parameterizations, 
as you would find e.g. in Wikipedia. Some distributions have a corresponding 
`Truncated` version which truncates the support of the distribution to an interval $[{\rm lower}, {\rm upper}]$.

#### Example:
```py
from kima import distributions

parameter_prior = distributions.Gaussian(10, 1)
```

??? example "`kima.distributions` API"

    ::: kima.distributions
        handler: python
        options:
          show_root_heading: false
          show_root_toc_entry: true
          heading_level: 2
          filters:
            - "!__"
            - "!Distribution"


Finally, even though it sits at the core of what **kima** does, is the [keplerian][kima.keplerian] function:


??? example "`kima.keplerian` API"
    
    ::: kima.keplerian