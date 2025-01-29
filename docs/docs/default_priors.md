=== "RVmodel"

    name | meaning | default prior
    ---- | ------- | -------------
    `Cprior`                  | systemic velocity                                  | Uniform($v_{\rm min}$, $v_{\rm max}$)
    `offsets_prior`           | between-instrument offsets                         | Uniform( -$\Delta v$, $\Delta v$ )
    `Jprior`                  | per-instrument jitter                              | ModifiedLogUniform($1$*, $\Delta v_{\rm max}$)
    `stellar_jitter_prior`    | stellar jitter                                     | Fixed(0)
    `slope_prior`             | slope of linear trend                              | Gaussian( 0, $\Delta v / \Delta t$ )
    `quadr_prior`             | coefficient of quadratic trend                     | Gaussian( 0, $\Delta v / \Delta t^2$ )
    `cubic_prior`             | coefficient of cubic trend                         | Gaussian( 0, $\Delta v / \Delta t^3$ )
    `nu_prior`                | degrees of freedom of Student-t likelihood         | LogUniform(2, 1000)


=== "GPmodel"

    name | meaning | default prior
    ---- | ------- | -------------
    `offsets_prior`           | between-instrument offsets                         | Uniform( -$\Delta v$, $\Delta v$ )
    `Jprior`                  | per-instrument jitter                              | ModifiedLogUniform($1$*, $\Delta v_{\rm max}$)
    `slope_prior`             | slope of linear trend                              | Gaussian( 0, $\Delta v / \Delta t$ )
    `quadr_prior`             | coefficient of quadratic trend                     | Gaussian( 0, $\Delta v / \Delta t^2$ )
    `cubic_prior`             | coefficient of cubic trend                         | Gaussian( 0, $\Delta v / \Delta t^3$ )
    `eta1_prior`              | GP "amplitude"                                     | LogUniform( 0.1, $\Delta v_{\rm max}$ )
    `eta2_prior`              | GP correlation timescale                           | LogUniform(1, $\Delta t$)
    `eta3_prior`              | GP period                                          | Uniform(10, 40)
    `eta4_prior`              | recurrence timescale or (inverse) harmonic complexity | Uniform(0.2, 5.0)


