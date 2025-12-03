---
comments: true
---

# Models

## Types of models

**kima** currently implements dedicated models for different analyses of
a given dataset. The models share a common organization, but each has its own
parameters, priors, and settings.

- [RVmodel][kima.RVmodel]  
  Models the RVs with a sum-of-Keplerians

- [GPmodel][kima.GPmodel]  
  Models the RVs with a sum-of-Keplerians plus a correlated noise component
  given by a Gaussian process

- [RVFWHMmodel][kima.RVFWHMmodel]  
  Models the RVs together with the FWHM as an activity indicator, including a
  Gaussian process for the activity signal

- [BINARIESmodel][kima.BINARIESmodel]  
  This includes tidal and relativistic effects as well as apsidal precession of
  the binary’s orbit (applicable for circumbinary planets), and can also fit 
  double-lined binary data. More details are available in
  [Baycroft et al. (2023)](https://ui.adsabs.harvard.edu/abs/2023MNRAS.521.1871B/abstract){:target="_blank"}.

- [GAIAmodel][kima.GAIAmodel]  
  Models Gaia epoch astrometry with a 5-parameter astrometric solution plus a 
  sum-of-Keplerians
  
- [RVGAIAmodel][kima.RVGAIAmodel]  
  Models RVs as well as Gaia epoch astrometry simultaneously with shared
  Keplerians

- TRANSITmodel (coming soon)

To use a given model, just instantiate an object of the respective class
providing the necessary options and a dataset

```cpp title="kima_setup.py"
from kima import RVmodel, GPmodel

data = ...

model = RVmodel(fix=True, npmax=1, data=data)
# or
model = GPmodel(fix=True, npmax=1, data=data)
```


???+ info

    The arguments to the models can be provided as positional arguments, as in
    ```py
    RVmodel(True, 1, data)
    ```
    but this is discouraged for being less readable. 
    Note, however, that there are not default values for these arguments, 
    as the keyword syntax could lead to believe.


<!--  -->


Each model has its own parameters and particular settings, 
which are described in detail in the [API](/docs/API) page.
Below we include a more mathematical description of each model.


=== "RVmodel"

    This is the most basic model, but already allows for an in-depth analysis 
    of an RV dataset. In terms of general settings, we can define

    ```python
    model = RVmodel(fix=True, npmax=1, data=data) # (4)

    model.trend = False / True
    model.degree = 0 / 1 / 2 / 3 # (1)
    
    model.set_known_object(1) # (2)

    model.studentt = False / True # (3)
    ```

    1.  sets up a long-term trend in the RVs, of a given degree. This is a 
    linear trend for `degree=1`, a quadratic for `degree=2`, and a cubic for 
    `degree=3`. Higher degree trends are not implemented.

    2. In known object (KO!) mode, **kima** considers some Keplerians as part 
    of a "background" model (see below). This can be useful when modelling 
    transiting planets or simply planets with better-known orbital parameters. 

    3. Whether to use a Student-t distribution as the likelihood, instead of a
    Gaussian. If this is set to `True`, the degrees of freedom of the
    Student-t distribution can be estimated from the data. This is sometimes
    useful when the RV data is suspected to contain (a few) strong outliers. 
    See the [example](/examples/student-t) for more information.

    4. See [below](#the-np-planets) for more information


=== "GPmodel"

    This model considers a Gaussian process as a surrogate model for the 
    stellar activity signal present in the RVs. It uses the now standard 
    quasi-periodic kernel[^1] with four hyper-parameters:

    $$ k(t_i, t_j) = \eta_1^2
                     \exp\left[-\frac{(t_i - t_j)^2}{2\eta_2^2}\right]
                     \exp\left[-\frac{2\sin^2\left(\frac{\pi (t_i - t_j)}{\eta_3}\right)}{\eta_4^2}\right]
    $$

    Most of the same settings as for the `RVmodel` are available, except for
    `studentt` which doesn't apply in this case.

      ```python
      model = GPmodel(fix=True, npmax=1) # (3)

      model.trend = False / True
      model.degree = 0 / 1 / 2 / 3 # (1)

      model.set_known_object(1) # (2)      
      ```

      1.  sets up a long-term trend in the RVs, of a given degree. This is a 
      linear trend for `degree=1`, a quadratic for `degree=2`, and a cubic for 
      `degree=3`. Higher degree trends are not implemented.

      2. In known object (KO!) mode, **kima** considers some Keplerians as part 
      of a "background" model (see below). This can be useful when modelling 
      transiting planets or simply planets with better-known orbital parameters. 

      3. See [below](#the-np-planets) for more information


---

## The Np planets


**kima** can model Keplerian functions, the RV signals induced by planets. But
the distinguishing feature is that the code can actually model a number $N_p$ of
Keplerians, and *this number does not need to be fixed a priori*. In other
words, **kima** estimates the joint posterior distribution for $N_p$ and the
orbital parameters of the planets $\theta$, given some data $ \mathcal{D} $:

$$ p(N_p, \theta | \mathcal{D} ) $$

So $N_p$ is actually a free parameter in the model (if we want). It is somewhat
of a special parameter, in that it only takes discrete values, but it's
otherwise similar to other parameters in the model. In particular, it needs a
prior distribution as well.

This part of the model can be set with the first two arguments to the constructors

```py
model = RVmodel(fix=False, npmax=2, data=data)
```

which define whether or not $N_p$ is fixed and its value or prior distribution

$$
    N_p 
    \begin{cases}
        = \texttt{npmax},                       & \text{if}\:\texttt{fix}\\
        \sim \mathcal{U}(0, \texttt{npmax}),    & \text{otherwise}
    \end{cases}
$$


<!-- <details class="example">
<summary>check this out!</summary>

```python {marimo}
import marimo as mo
import matplotlib.pyplot as plt
from numpy import zeros, arange
plt.style.use('fast')
```

```python {marimo}
Np = mo.ui.slider(0, 10)
fix = mo.ui.checkbox()
mo.md(f"`model = RVmodel(fix=`{fix}`, npmax=`{Np}`, data=data)`")
```

```python {marimo}
def plot_prior(Np, fix):
  pmf = zeros(11)
  if fix:
      pmf[Np] = 1
  else:
      pmf[:Np+1] = 1 / (Np + 1)
    
  plt.figure(figsize=(6, 3))
  #plt.set(xticks=arange(11), yticks=[0, 1], 
  #      xlabel="$N_p$", ylabel="prior", ylim=[0, 1.1])
  plt.stem(arange(11), pmf)
  return plt.gca()
```

```python {marimo}
Np.value
```

```python {marimo}
plot_prior(Np.value, fix.value)
#mo.mpl.interactive(fig)
```

</details>
 -->



When fitting radial velocity data, by default, each of the $N_p$ planets has 5 orbital parameters

$$
\theta = { P, K, e, M_0, \omega }
$$

corresponding to the orbital period, semi-amplitude, eccentricity, mean anomaly
at the epoch, and argument of periastron.

Some models include additional *per-planet* parameters: the
[BINARIESmodel][kima.BINARIESmodel] considers a linear precession parameter
$\dot\omega$ for the binary (which uses the Known Object mode, see below), and the BDmodel infers a mixture probability $\lambda$ for each
planet.

!!! info "units"

    - $P$ is in [days]
    - $K$ is in [m/s]
    - $e$ is unitless
    - $M_0$ and $ω$ are in radians

By default, the epoch is set to the middle of the observed times ($t_{\rm min} +
\Delta t / 2$), but it can be changed by setting the corresponding attribute of
the data:

```py
data.M0_epoch = ...
```

When fitting Gaia astrometric data, each of the $N_p$ planets has 7 parameters, either of

$$
\theta_{\rm orb} = { P, M_0, e, a0, \omega, \cos{i}, \Omega }\\
\theta_{\rm TI} = { P, M_0, e, A, B, F, G }
$$

depending on whether you chose to fit using the orbital parameters, or the linearised Thiele-Innes parameters.
Either of these can be chosen in the [GAIAmodel][kima.GAIAmodel], but in the [RVGAIAmodel][kima.RVGAIAmodel] the geometric 
parameters must be used.

!!! info "units"

    - $P$ is in [days]
    - $a0$ is in [mas]
    - $e$ and $cosi$ are unitless
    - $M_0$, $ω$, and $Ω$ are in radians

By default, the epoch is set to the Gaia DR4 reference epoch (tcb_jd = 2457936.875) but it can be changed by setting the corresponding attribute of
the data:

```py
data.M0_epoch = ...
```


---

## The "background" model


Besides the $N_p$ planets, **kima** models the RV observations as follows

- by default

$$
v_i \sim \mathcal{N} \left( v_{sys} \,,\: j^2+\sigma_i^2 \right)
$$

- if using a Student-t likelihood

$$
v_i \sim \mathcal{T} \left( v_{sys} \,,\: j^2+\sigma_i^2, \nu \right)
$$

- if using a Gaussian process


$$
v \sim \mathcal{GP} \left( \boldsymbol{\mu} = v_{sys} \,,\: \boldsymbol{\Sigma} = {\bf K} + (j^2+\sigma_i^2)\,\delta_{ij}\,{\bf I} \right)
$$

where $j$ is a *jitter* parameter that represents additional white noise
variations not accounted for by the individual uncertainties. 

When considering a long-term trend (by setting `trend=True`), instead of a
constant systemic velocity, we have

$$
\begin{aligned}
v_{sys} \rightarrow v_{sys} 
  & + \text{slope} \times (t-t_m) \qquad      \text{if} \: \texttt{degree} \ge 1\\
  & + \text{quadr} \times (t-t_m)^2 \quad\,   \text{if} \: \texttt{degree} \ge 2\\
  & + \text{cubic} \times (t-t_m)^3 \quad\:\: \text{if} \: \texttt{degree} = 3
\end{aligned}
$$

where $t_m$ is the mean of the times and *slope*, *quadr*, and *cubic* are free
parameters. See the [example]() for more information.


When using data from multiple instruments, **kima** adds RV offsets between
pairs of instruments as well as individual jitter parameters per instrument.


### Known object mode

As described [below](#prior-distributions), the priors for the orbital
parameters of the $N_p$ planets are all the same. 

To go around this limitation, we can add _known objects_ to the background
model, which are simply Keplerians for which we define individual priors. Each
of these Keplerians has the same 5 orbital parameters ${P, K, e, M_0, \omega}$,
in the same units. 

!!! note

    The number of _known objects_ is defined by the user and is always fixed,
    unlike $N_p$.

Most models can accomodate known objects which can be added to the model with
the following code

```py
model.set_known_object(1)

model.KO_Pprior = [kima.distributions...]
model.KO_Kprior = [...]
model.KO_eprior = [...]
model.KO_phiprior = [...]
model.KO_wprior = [...]
```


---


## Prior distributions

As in any Bayesian analysis, **kima** needs a set of priors for the model
parameters. If we don't explicitly set the priors, default ones will be 
used and the model will run. But sometimes we will want to set custom priors for some parameters.

To change specific priors, we just need to re-assign some attributes of the models,
using the probability distributions defined in [kima.distributions][].
Admittedly, some of these attributes might have rather undescriptive names, 
but they are still documented individually.

For example, let's re-define the priors for the jitter and for the orbital
periods of the $N_p$ planets

```py
from kima.distributions import Uniform, Gaussian

model = RVmodel(...)

model.Jprior = Uniform(1, 20)  # (1)

model.conditional.Pprior = Gaussian(15, 0.1)
```

1. :warning: Note that this is a uniform distribution from 1 m/s to 20 m/s,
unlike the `scipy.stats.uniform` distribution which would have support [1, 21] !

!!! warning

    While it might seem counter-intuitive at first, **the priors for the
    orbital parameters are *the same* for all the $N_p$ planets**. This is intentional!
    But if this is not what you want, consider using the [known object](#known-object-mode) feature.

!!! info "`conditional` priors"

    The priors for the orbital parameters of the $N_p$ Keplerians
    are included in the `model.conditional` object because these priors are 
    _conditional on_ having those Keplerians.


In the example above, we specify the prior distribution and its parameters.
The list of currently implemented distributions is described [here][kima.distributions].
If you need a distribution that is not yet implemented, consider opening an
[issue](https://github.com/kima-org/kima/issues){:target="_blank"}.



### Default priors

Below is the list of default priors which are used if not explicitly re-defined.

{%
    include-markdown "./default_priors.md"
    start="<!--intro-start-->"
    end="<!--intro-end-->"
%}


And for the orbital parameters

=== "orbital parameters"

    - orbital period(s), `conditional.Pprior`  
      LogUniform(1, $\Delta t$)
    
    - semi-amplitude(s), `conditional.Kprior`  
      Uniform(0, $\Delta v$)

    - orbital eccentricity(ies), `conditional.eprior`  
      Uniform(0, 1)

    - mean anomaly at the epoch, `conditional.phiprior`  
      Uniform(0, 2$\pi$)

    - argument of pericenter, `conditional.wprior`  
      Uniform(0, 2$\pi$)

=== " "


!!! question "How many parameters?"

    The number of free parameters can change substantially depending on the model, 
    the different settings, and the specific parameter priors 
    (which can be [Fixed][kima.distributions.Fixed], for example).
    In essence, the number of free parameters is usually not a very useful quantity by itself.





## References

[^1]: Rasmussen, C.E. and Williams, C.K.I. (2006) *Gaussian processes for machine learning*.   
      The MIT Press, ISBN 0-262-18253-X  
      [link](https://gaussianprocess.org/gpml/), 
      [PDF](https://gaussianprocess.org/gpml/chapters/RW.pdf)


<script src="https://cdn.jsdelivr.net/npm/@marimo-team/marimo-snippets@1"></script>
