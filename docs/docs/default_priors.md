<!-- automatically generated with -->
<!-- `python get_default_priors.py` -->

In the tables below, the following abbreviations are used:

- $\Delta t$ is the timespan of the data
- $\Delta v$ is the span of the RVs
- $\Delta {\rm FWHM}$ is the span of the FWHM
- $\mathcal{O}(x)$ is order of magnitude of $x$


=== "RVmodel"

    name | meaning | default prior
    ---- | ------- | -------------
    `beta_prior`              | Gaussian(0, 1)                                               | activity indicator coefficients                   
    `offsets_prior`           | Uniform(-$\Delta v$, $\Delta v$)                             | between-instrument offsets                        
    `stellar_jitter_prior`    | Fixed(0)                                                     | stellar jitter                                    
    `slope_prior`             | Gaussian(0, $\mathcal{O} \left(\frac{ \Delta v }{ \Delta t} \right)$) | slope of linear trend                             
    `quadr_prior`             | Gaussian(0, $\mathcal{O} \left(\frac{ \Delta v }{ \Delta t^2} \right)$) | coefficient of quadratic trend                    
    `cubic_prior`             | Gaussian(0, $\mathcal{O} \left(\frac{ \Delta v }{ \Delta t^3} \right)$) | coefficient of cubic trend                        
    `nu_prior`                | LogUniform(2, 1000)                                          | degrees of freedom of Student-t likelihood        


=== "GPmodel"

    name | meaning | default prior
    ---- | ------- | -------------
    `beta_prior`              | Gaussian(0, 1)                                               | activity indicator coefficients                   
    `offsets_prior`           | Uniform(-$\Delta v$, $\Delta v$)                             | between-instrument offsets                        
    `slope_prior`             | Gaussian(0, $\mathcal{O} \left(\frac{ \Delta v }{ \Delta t} \right)$) | slope of linear trend                             
    `quadr_prior`             | Gaussian(0, $\mathcal{O} \left(\frac{ \Delta v }{ \Delta t^2} \right)$) | coefficient of quadratic trend                    
    `cubic_prior`             | Gaussian(0, $\mathcal{O} \left(\frac{ \Delta v }{ \Delta t^3} \right)$) | coefficient of cubic trend                        
    `eta1_prior`              | LogUniform(0.1, $\Delta v_{\rm max}$)                        | GP "amplitude"                                    
    `eta2_prior`              | LogUniform(1, $\Delta t$)                                    | GP correlation timescale                          
    `eta3_prior`              | Uniform(10, 40)                                              | GP period                                         
    `eta4_prior`              | Uniform(0.2, 5.0)                                            | recurrence timescale                              


=== "RVFWHMmodel"

    name | meaning | default prior
    ---- | ------- | -------------
    `slope_prior`             | Gaussian(0, $\mathcal{O} \left(\frac{ \Delta v }{ \Delta t} \right)$) | slope of linear trend                             
    `quadr_prior`             | Gaussian(0, $\mathcal{O} \left(\frac{ \Delta v }{ \Delta t^2} \right)$) | coefficient of quadratic trend                    
    `cubic_prior`             | Gaussian(0, $\mathcal{O} \left(\frac{ \Delta v }{ \Delta t^3} \right)$) | coefficient of cubic trend                        
    `eta1_prior`              | LogUniform(0.1, $\Delta v_{\rm max}$)                        | GP "amplitude"                                    
    `eta2_prior`              | LogUniform(1, $\Delta t$)                                    | GP correlation timescale                          
    `eta3_prior`              | Uniform(10, 40)                                              | GP period                                         
    `eta4_prior`              | Uniform(0.2, 5.0)                                            | recurrence timescale                              
    `Cfwhm_prior`             | Uniform(FWHM$_{\rm min}$, FWHM$_{\rm max}$)                  | "systemic" FWHM                                   
    `Jfwhm_prior`             | complicated                                                  | jitter in FWHM                                    
    `slope_fwhm_prior`        | Gaussian(0, $\mathcal{O} (\frac{\Delta {\rm FWHM}}{\Delta t})$) |                                                   
    `quadr_fwhm_prior`        | Gaussian(0, $\mathcal{O} (\frac{\Delta {\rm FWHM}}{\Delta t^2})$) |                                                   
    `cubic_fwhm_prior`        | Gaussian(0, $\mathcal{O} (\frac{\Delta {\rm FWHM}}{\Delta t^3})$) |                                                   
    `eta1_fwhm_prior`         | LogUniform(0.1, $\Delta {\rm FWHM}$)                         |                                                   
    `eta2_fwhm_prior`         | LogUniform(1, $\Delta t$)                                    |                                                   
    `eta3_fwhm_prior`         | Uniform(10, 40)                                              |                                                   
    `eta4_fwhm_prior`         | Uniform(0.2, 5.0)                                            |                                                   


