# Models

## Types of models

**kima** currently implements dedicated models for different analyses of
a given dataset. The models share a common organization, but each has its own
parameters (and thus priors) and settings.

- RVmodel  
  models the RVs with a sum-of-Keplerians

- GPmodel  
  models the RVs  with a sum-of-Keplerians plus a correlated noise component
  given by a Gaussian process

- RVFWHMmodel  
  models the RVs together with the FWHM as an activity indicator, including a
  Gaussian process for the activity signal

- BINARIESmodel  
  this includes tidal and relativistic effects as well as apsidal precession of
  the binary’s orbit (applicable for circumbinary planets), and can also fit 
  double-lined binary data.


The type of model to be used is set by defining a *constructor* in the
`kima_setup` file

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


Since each model has its own parameters and settings, they are described
separately below.


=== "RVmodel"

    This is the most basic model, but already allows for an in-depth analysis 
    of an RV dataset. In terms of general settings, we can define

    === "C++"

        ```c++ title="kima_setup.cpp"
        RVmodel::RVmodel() : fix(true), npmax(1) // (4)
        {
          trend = false / true;
          degree = 0 / 1 / 2 / 3; // (1)

          known_object = false / true; // (2)
          n_known_object = 0 / ... ;
          
          studentt = false / true; // (3)
        }
        ```

        1. Sets up a long-term trend in the RVs, of a given degree. This is a 
        linear trend for `degree=1`, a quadratic for `degree=2`, and a cubic for 
        `degree=3`. Higher degree trends are not implemented.

        2. In known object (KO!) mode, **kima** considers some Keplerians as part 
        of a "background" model (see below). This can be useful when modelling 
        transiting planets or simply planets with better-known orbital parameters. 

        3. Whether to use a Student-t distribution as the likelihood, instead of a
        Gaussian. If this is set to `true`, the degrees of freedom of the
        Student-t distribution can be estimated from the data. This is sometimes
        useful when the RV data is suspected to contain (a few) strong outliers. 
        See the [example](/examples/student-t) for more information.

        4. See [below](#the-np-planets) for more information


    === "Python"

        ```python title="kima_setup.py"
        model = RVmodel(fix=True, npmax=1) # (4)

        model.trend = False / True
        model.degree = 0 / 1 / 2 / 3 # (1)
        
        model.known_object = False / True # (2)
        model.n_known_object = 0 / ...
        
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
    stellar activity signal present in the RVs. We can specify different kernels
    each with its own hyper-parameters and infer their posterior as well. By 
    default, the standard quasi-periodic kernel is used, with four 
    hyper-parameters:

    $$ k(t_i, t_j) = \eta_1^2
                     \exp\left[-\frac{t_i - t_j}{\eta_2^2}\right]
                     \exp\left[-\frac{\sin^2\left(\frac{t_i - t_j}{\eta_3}\right)}{2 \eta_4^2}\right]
    $$

    Most of the same settings as for the RVmodel are available, except for the
    `studentt` which doesn't apply in this case.

    === "C++"

        ```c++ title="kima_setup.cpp"
        GPmodel::GPmodel() : fix(true), npmax(1) // (4)
        {
          trend = false / true;
          degree = 0 / 1 / 2 / 3; // (1)

          known_object = false / true; // (2)
          n_known_object = 0 / ... ;
        }
        ```

        1. Sets up a long-term trend in the RVs, of a given degree. This is a 
        linear trend for `degree=1`, a quadratic for `degree=2`, and a cubic for 
        `degree=3`. Higher degree trends are not implemented.

        2. In known object (KO!) mode, **kima** considers some Keplerians as part 
        of a "background" model (see below). This can be useful when modelling 
        transiting planets or simply planets with better-known orbital parameters. 

        4. See [below](#the-np-planets) for more information


    === "Python"

        ```python title="kima_setup.py"
        model = GPmodel(fix=True, npmax=1) # (4)

        model.trend = False / True
        model.degree = 0 / 1 / 2 / 3 # (1)
        
        model.known_object = False / True # (2)
        model.n_known_object = 0 / ...
        ```

        1.  sets up a long-term trend in the RVs, of a given degree. This is a 
        linear trend for `degree=1`, a quadratic for `degree=2`, and a cubic for 
        `degree=3`. Higher degree trends are not implemented.

        2. In known object (KO!) mode, **kima** considers some Keplerians as part 
        of a "background" model (see below). This can be useful when modelling 
        transiting planets or simply planets with better-known orbital parameters. 

        4. See [below](#the-np-planets) for more information


---

## The Np planets


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
for the model constructors

=== "C++"

    ```c++ title="kima_setup.cpp"
    RVmodel::RVmodel() : fix(false) npmax(2)
    {
        ...
    }
    ```

=== "Python"

    ```py title="kima_setup.py"
    model = RVmodel(fix=False, npmax=2)
    ```

which define whether of not $N_p$ is fixed and its value or prior distribution

$$
    N_p 
    \begin{cases}
        = \texttt{npmax},                       & \text{if}\:\texttt{fix}\\
        \sim \mathcal{U}(0, \texttt{npmax}),    & \text{otherwise}
    \end{cases}
$$

By default, each of the $N_p$ planets has 5 orbital parameters

$$
\theta = { P, K, e, M_0, \omega }
$$

corresponding to the orbital period, semi-amplitude, eccentricity, mean anomaly
at the epoch, and argument of periastron.

Some models include additional *per-planet* parameters: the BINARIESmodel
considers a linear precession parameter $\dot\omega$ and the BDmodel infers a
mixture probability $\lambda$ for each planet.

!!! info "units"

    $P$ is in [days], $K$ is in [m/s], $e$ is unitless,
    and $M_0$ and $ω$ are in radians.

By default, the epoch is set to the first observed time, but it can be changed
inside the constructor by setting the variable `M0_epoch`.


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



---


## Prior distributions

As in any Bayesian analysis, **kima** needs a set of priors for the model
parameters. If we don't explicitly set the priors in the `kima_setup` file, a
default prior will be used and the model will run. But sometimes (most times?)
we will want to set custom priors for some parameters.

To change specific priors, we just need to re-define some variables inside the
model constructor.

!!! warning
    Note below that, on the C++ side, the priors for the planet orbital
    parameters require the `conditional` object, while the priors for the other
    parameters do not. In Python, this is automatically taken care of.


For example, let's re-define the priors for the jitter and for the orbital
periods of the $N_p$ planets

=== "C++"

    ```c++
    RVmodel::RVmodel()
    {
        // define new prior for the extra white noise parameter (jitter), j
        Jprior = make_prior<Uniform>(1.0, 20.0);

        // define new prior for the orbital period(s)
        // first, get the conditional prior object
        auto conditional = planets.get_conditional_prior();
        conditional->Pprior = make_prior<Gaussian>(15.0, 0.1); // in days
    }
    ```

=== "Python"

    ```py
    from scipy.stats import uniform, norm

    model = RVmodel()

    # define new prior for the extra white noise parameter (jitter), j
    model.priors.J = uniform(1, 20)  # (1)

    # define new prior for the orbital period(s)
    model.priors.P = norm(15, 0.1)
    ```

    1. :warning: Note that this is a uniform distribution from 1 m/s to 20 m/s,
    unlike the `scipy` distribution which would have support [1, 21] !


While it might seem counter-intuitive at first, in **kima** the priors for the
orbital parameters are *the same* for all $N_p$ planets. This is intentional!
But if this is not what you want, consider using the known object feature.

In the example above, we specify the prior distribution and its parameters.
**kima** (and DNest4) currently implement the following distributions:


| in C++               | in Python                    | arguments                     | link                                                                                       |
|----------------------|------------------------------|-------------------------------|--------------------------------------------------------------------------------------------|
| `Uniform`            | `scipy.stats.uniform`        | lower, upper                  | [wiki](https://en.wikipedia.org/wiki/Continuous_uniform_distribution), [scipy](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.uniform.html)<sup>!</sup> |
| `LogUniform`         | `scipy.stats.loguniform`     | lower, upper                  | [scipy](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.loguniform.html)  |
| `ModifiedLogUniform` |                              | knee, upper                   |                                                                                            |
| `Rayleigh`           | `scipy.stats.rayleigh`       | scale                         |                                                                                            |
| `Triangular`         |                              | lower, center, upper          | [wiki](https://en.wikipedia.org/wiki/Triangular_distribution), [scipy](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.triang.html) |
| `Laplace`            |                              | center, width                 | [wiki](https://en.wikipedia.org/wiki/Laplace_distribution)                                 |
| `Kumaraswamy`        | `pykima.priors.kumaraswamy`  | alpha, beta                   | [wiki](https://en.wikipedia.org/wiki/Kumaraswamy_distribution)                             |
| `Gaussian`           | `scipy.stats.norm`           | mean, stdev                   | [wiki](https://en.wikipedia.org/wiki/Normal_distribution), [scipy](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.norm.html) |
| `Exponential`        |                              | mean                          | [wiki](https://en.wikipedia.org/wiki/Exponential_distribution) |
| `Cauchy`             | `scipy.stats.cauchy`         | location, scale               | [wiki](https://en.wikipedia.org/wiki/Cauchy_distribution), [scipy](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.cauchy.html) |
| `Fixed`              |                              | value                         | |
| `TruncatedGaussian`  |                              | mean, stdev, lower, upper     | |
| `TruncatedCauchy`    |                              | location, scale, lower, upper | |


If you need a distribution that is not yet implemented, consider opening an
[issue](https://github.com/j-faria/kima/issues){:target="_blank"}.



###### Checking if the priors are correct

For any **kima** model, it's very easy to check if the priors are set up
correctly. In the `OPTIONS` file, set the maximum number of levels to 1, and run
the model as usual. With this setting, DNest4 will sample directly from the
priors and ignore the likelihood.

The `kima-checkpriors` script is provided with the `pykima` package to make
histograms of the samples of each parameter. Simply call it with the column or
the name of the parameter you want to check (look inside `sample.txt` to see
which one is which)

For example

```sh
kima-checkpriors 1
```

might print

    Histogram of column 1: jitter
    number of samples: 10000
    max value: 1999.744937
    min value: 0.000128

and display the histogram of the prior samples:

![](https://image.ibb.co/bKtfEd/priorJ.png)


###### Default priors

Below is the list of default priors which are used if not explicitly re-defined
in the model constructor.

<details markdown=1><summary>expand</summary>

  - orbital periods and semi-amplitudes  
    units: days and m/s, respectively

    ```c++
    // if hyperpriors = false (default)
    Pprior = make_shared<LogUniform>(1.0, 1e5);
    Kprior = make_shared<ModifiedLogUniform>(1.0, 1e3);

    // if hyperpriors = true
    Pprior = make_shared<Laplace>(exp(log_muP), wP);
    Kprior = make_shared<Exponential>(exp(log_muK));
    ```

  - orbital eccentricities
    ```c++
    eprior = make_shared<Uniform>(0, 1);
    ```

    another good option could be
    ```c++
    eprior = make_shared<Kumaraswamy>(0.867, 3.03);
    ```
    which approximates the Beta distribution proposed by Kipping (2013).

  - orbital phase and argument of periastron  
    units: radians

    ```c++
    phiprior = make_shared<Uniform>(0, 2*PI);
    
    wprior = make_shared<Uniform>(0, 2*PI);
  ```

  - systemic velocity  
    units: m/s
    
    ```cpp
    Cprior = make_prior<Uniform>(data.get_RV_min(), data.get_RV_max());
    ```

  - additional white noise, or *jitter*  
    units: m/s
    
    ```c++
    knee = min(1.0, 0.1*data.get_max_RV_span()) 
    upper = data.get_max_RV_span()

    Jprior = make_prior<ModifiedLogUniform>(knee, upper);
    ```

  - ccoefficients of the trend (if trend=true)  
    units: m/s/day, m/s/day², m/s/day³
  ```c++
  slope_prior = make_prior<Gaussian>(0.0, pow(10, data.get_trend_magnitude(1)));
  quadr_prior = make_prior<Gaussian>(0.0, pow(10, data.get_trend_magnitude(2)));
  cubic_prior = make_prior<Gaussian>(0.0, pow(10, data.get_trend_magnitude(3)));
  ```

  - between-instrument offsets
    units: m/s

    ```c++
    lower = -data.get_RV_span();
    upper = data.get_RV_span();
    offsets_prior = make_prior<Uniform>(lower, upper);
    ```

  {% katexmm %}

  - GP hyperparameters  
    units: $\eta_1$ in m/s, $\eta_2$ and $\eta_3$ in days

    ```c++
    log_eta1_prior = make_prior<Uniform>(-5, 5);

    eta2_prior = make_prior<LogUniform>(1, 100);

    eta3_prior = make_prior<Uniform>(10, 40);

    log_eta4_prior = make_prior<Uniform>(-1, 1);
    ```

  {% endkatexmm %}

  - degrees of freedom for Student-t likelihood

    ```c++
    nu_prior = make_prior<LogUniform>(2, 1000);
    ```

  - moving average parameters

    ```c++
    sigmaMA_prior = make_prior<Uniform>(-1, 1);
    tauMA_prior = make_prior<LogUniform>(1, 100);
    ``` 

  - hyperparameters for orbital period and semi-amplitude hierachical priors  
    only if `hyperpriors=true`

    ```c++
    log_muP_prior = make_prior<TruncatedCauchy>(log(365), 1.0, log(365)-21, log(365)+21);
    
    wP_prior = make_prior<Uniform>(0.1, 3.0);
    
    log_muK_prior = make_prior<TruncatedCauchy>(0.0, 1.0, -21.0, 21.0);
    ```

  - correlation coefficients with activity indicators

    ```c++
    betaprior = make_prior<Gaussian>(0, 1);
    ```

</details>


