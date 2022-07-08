# Models

## General settings

Every model in **kima** starts with a few general settings

=== "C++"

    ```c++ title="kima_setup.cpp"
    const bool GP
    const bool MA
    const bool hyperpriors
    const bool trend
    const int degree
    const bool multi_instrument
    const bool known_object
    const int n_known_object
    const bool studentt
    ```

=== "Python"

    ```python title="kima_setup.py"
    model = RVmodel()

    model.GP = True
    model.MA = True
    model.hyperpriors = True
    model.trend = True
    model.degree = 1
    model.multi_instrument = True
    model.known_object = True
    model.n_known_object = 1
    model.studentt = True
    ```


which **always** need to be defined.

!!! bug ""
    yes, this is annoying, I'm working on a solution!

While some of these settings are self-explanatory, others can get be a bit
tricky to understand. Here's a short explanation for each one.


`GP`

: Whether to use a Gaussian process (GP) as a model for correlated noise in
  the RVs.


`MA`

: Whether to use a Moving Average as a model for correlated noise in the RVs.

    !!! caution ""

        the moving average model is still experimental


`hyperpriors`

: Whether to use hyper-priors for the orbital periods and for the
  semi-amplitudes of the planets. See the section on
  [priors](#setting-the-priors) for more information.


`trend` and `degree`

: Consider a long-term trend in the RVs, of a given degree. This is a linear
  trend for `degree=1`, a quadratic for `degree=2`, and a cubic for `degree=3`.
  Higher degree trends are not implemented.


`multi_instrument`

: Whether the RV data comes from multiple instruments. See the
  [example]({{site.baseurl}}/blog/multi-instrument) for more details.

    !!! caution ""

        a typical source of issues is loading multiple datasets with the
        `load_multi` function (see below) but forgetting to set `multi_instrument =
        true`.


`known_object` and `n_known_object`

: In known object (KO!) mode, **kima** considers some Keplerians as part of a
  "background" model, not included in the $N_p$ other planets. This can be
  useful when modeling transiting planets or simply planets with better-known
  orbital parameters. 


`studentt`

: Whether to use a Student-t distribution as the likelihood, instead of a
  Gaussian. If this is set to `true`, the degrees of freedom $\nu$ of the
  Student-t distribution can be estimated from the data. This is sometimes
  useful when the RV data is suspected to contain (a few) strong outliers. See
  the [example]({{site.baseurl}}/blog/student-t) for more information.


<!--  -->

## Types of models

**kima** currently implements two kinds of models

- RVmodel  
  which models the RVs with a sum-of-Keplerians

- RVFWHMmodel  
  which models the RVs together with the FWHM as an activity indicator

The type of model to be used is set by defining a *constructor* like

=== "C++"
    ```cpp title="kima_setup.cpp"
    RVmodel::RVmodel()
    {
        ...
    }
    ```
=== "Python"
    ```cpp title="kima_setup.py"
    from pykima.models import RVmodel
    model = RVmodel()
    ```


or 

=== "C++"
    ```cpp title="kima_setup.cpp"
    RVFWHMmodel::RVFWHMmodel()
    {
        ...
    }
    ```
=== "Python"
    ```cpp title="kima_setup.py"
    from pykima.models import RVFWHMmodel
    model = RVFWHMmodel()
    ```

???+ info

    A *constructor* in C++ is a special function that instantiates an object, in
    this case of type `RVmodel` or `RVFWHMmodel`. If this sounds weird, think of
    it as the `__init__` function in a Python class. If this also sounds weird,
    just think of it as a simple function where we implement the model.


<!--  -->


## The $N_p$ planets


**kima** can model Keplerian functions, the RV signals induced by planets. But
the distinguishing feature is that the code can actually model a number $N_p$ of
Keplerians, and *this number does not need to be fixed a priori*. In other
words, **kima** estimates the joint posterior distribution for $N_p$ and the
orbital parameters of the planets $\theta$, given some data $\mathcal{D}$:

$$ p(N_p, \theta | \mathcal{D} ) $$

So $N_p$ is actually a free parameter in the model (if we want). It is somewhat
of a special parameter, in that it only takes discrete values, but it's
otherwise similar to other parameters in the model. In particular, it needs a
prior distribution as well.

This part of the model in **kima** can be set with a special pair of parameters
for the model constructor

```c++
RVmodel::RVmodel() : fix(false) npmax(2)
{
    ...
}
```

which define whether of not $N_p$ is fixed and its value or prior distribution

$$
    N_p 
    \begin{cases}
        = \texttt{npmax},                       & \text{if}\:\texttt{fix}\\
        \sim \mathcal{U}(0, \texttt{npmax}),    & \text{otherwise}
    \end{cases}
$$

Each of the $N_p$ planets has 5 orbital parameters

$$
\theta = { P, K, e, M_0, \omega }
$$

corresponding to the orbital period, semi-amplitude, eccentricity, mean anomaly
at the epoch, and argument of periastron.

!!! info "units"

    $P$ is in [days], $K$ is in [m/s], $e$ is unitless,
    and $M_0$ and $ω$ are in radians.

By default, the epoch is set to the first observed time, but it can be changed
inside the constructor by setting the variable `M0_epoch`.



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

When considering a long-term trend (by setting `trend=true`), instead of a
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


## Model parameters


!!! question "How many parameters?"

    The number of free parameters can change substantially with each model, 
    with the different settings, and with the specific parameter priors.
