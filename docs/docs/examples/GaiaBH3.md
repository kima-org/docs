# Gaia BH3

This example takes you through the use of the
[GAIAmodel](/docs/API/kima/kima#GAIAmodel) and
[RVGAIAmodel](/docs/API/kima/kima#RVGAIAmodel) to analyse the Gaia epoch
astrometry and radial velocities from Gaia BH3 ([Gaia Collaboration et al.
2024](https://doi.org/10.1051/0004-6361/202449763)).

The example is divided into three parts: the first two using the `GAIAmodel` on
only the astrometric data, and the thrid including the radial velocities with
the `RVGAIAmodel`.


First lets import **kima** and the three examples


```python
import kima
from kima.examples.GaiaBH3 import GAIA, GAIA_free, RVGAIA
from kima.examples.GaiaBH3 import Gaia_astrometry_BH3_datafile, Gaia_RVs_BH3_datafile
```


Let's load the model and check the reference epoch being used for the
astrometry: we use the reference epoch for Gaia DR4, 2457936.875, but notice
that here we have subtracted 2400000 from the times. This is just to make plots
more legible, but there is also the option within the plot functions to do this later. 


```python
model = GAIA_free(run=False)
```


```python
model.data.M0_epoch
```

    57936.875


The functions from the examples can be called with `run=True` to run the models
directly. But let's go through setting up the model in each case to explore the
available options.


The data class is `GAIAdata` and we import the `GAIAmodel`. We
also import `RVData` and `RVGAIAmodel` since we will be using these later on,
and some distributions to set up the priors.


```python
from kima import RVData, GAIAdata
from kima import GAIAmodel, RVGAIAmodel
from kima.distributions import (
    Uniform,
    Gaussian,
    ModifiedLogUniform,
    LogUniform,
    Kumaraswamy,
    Fixed,
)
```


## GAIAmodel with $Np$ free


Below is the setup for a model of the astrometric data with the number of Keplerians, $Np$, free
between 0 and 2. This is a very broad search for the BH3 signal, but given that the signal is so
large, it will probably be a bit inefficient. This is because nested sampling can struggle when the
prior width is many orders of magnitude larger than the posterior, leading to inefficient sampling.

One way to *nudge* the sampler into the right direction is to fix the jitter to a high value (in
this case 0.5 mas), basically broadening the likelihood (and thus the posterior). If you're running
it yourself, try varying the jitter value to see if the model converges.

First, let's load the data from the file provided with the example, which looks like this


```
tcb	w	sigw	psi	pf
---	-	----	---	--
56958.110978	146.558250	0.031323	-1.030560	0.708280
56958.184993	146.136000	0.022889	-1.031674	0.708473
...
```


and can be loaded as 


```python
data = GAIAdata(Gaia_astrometry_BH3_datafile, skip=2, units="mas")
```


We then set the epoch to the relevant Gaia reference time (here in TCB -
2400000), as mentioned before


```python
data.M0_epoch =  57936.875 
```


Now we can create the model and set up the priors


```python
model = GAIAmodel(fix=False, npmax=2, data=data)
```


For the first pass we are going to fix the jitter quite high, as explained above


```python
model.Jprior = Fixed(0.5)  # in mas
```


We use broad priors for the period and semi-major axis, and default priors for
$\omega$, $\Omega$, and $\phi$


```python
model.conditional.Pprior = LogUniform(100.0, 10000.0)
model.conditional.aprior = ModifiedLogUniform(0.1, 100.0)
model.conditional.eprior = Kumaraswamy(0.867, 3.03)
model.conditional.cosiprior = Uniform(-1, 1)
```


For the priors for the 5-parameter astrometric solution, we use values from the
Gaia catalogue with appropriately wide priors to not bias the results


```python
model.da_prior = Gaussian(4.2,0.4)
model.dd_prior = Gaussian(2.4, 0.3)
model.mua_prior = Gaussian(-30, 80)
model.mud_prior = Gaussian(150, 80)
model.parallax_prior = Uniform(1.3, 1.9)
```


And we also set the stellar mass, RA, and DEC. These are not needed for the
analysis but will be useful for handling of the posteriors and making plots


```python
model.star_mass = 1.2  # in solar masses
model.RA = 294.82796478104
model.DEC = 14.931669719919999
```


<div class="admonition note">
<div class="admonition-title">Note</div>
<p style="margin-top: 1em">
    We could also use a Thiele-Innes parametrisation instead of the (default) geometric 
    elements. In this case, we would not set priors for a or cosi.
</p>
<div class="highlight-ipynb hl-python">
<pre>
<span>model.conditional.thiele_innes <span class="o">=</span> <span class="kc">True</span></span>
<span>model.thiele_innes <span class="o">=</span> <span class="kc">True</span></span>
</pre>
</div>
</div>


```python
kima.run(model, steps=100)
```


Now run the model and load the results, the number of steps can be reduced to speed up at the cost of a less well explored posterior


```python
model, res = GAIA_free(run=True, load=True, steps=100000)
```

    log(Z) = -82.88
    Information = 60.01 nats
    Effective sample size = 3645.0



Having loaded in the results, we can then plot the posterior for the number of
planets, confirming that we detect one orbit, that of GAIA BH3


```python
res.plot_posterior_np()
```

    Np probability ratios:  [1.47959184]


![png](GaiaBH3_files/GaiaBH3_Nphist_1.png)


We then obtain the maximum likelihood sample (with $Np$ restricted to 1) this will print the parameters of this posterior sample.


```python
p = res.maximum_likelihood_sample(Np=1)
```

    Sample with the highest likelihood value  (logL = -19.26)
    from samples with Np=1 only
    -> might not be representative of the full posterior distribution

    jitter:
      [0.5]
    number of planets:  1
    orbital parameters:           P         phi         ecc           a           w        cosi           W 
                         3800.88244     5.87764     0.71192    25.35025     1.35475    -0.35769     2.36097



We now plot that astrometric phase plot, which shows the parallactic motion as well as the orbit of BH3 and the along-scan residuals. Notice that since we fixed the jitter to being high, the residuals, including the jitter, show that it clearly larger than necessary to fit the data. In the orbit plot, the x shows the centre-of-mass and the dashed line connects it to the pericentre.

Since we use tcb-2400000 as the time units, we have to specify this so that this plot can funciton properly


```python
res.phase_plot(p,dates='tcb',date_sub=2400000,include_jitter=True);
```

![png](GaiaBH3_files/GaiaBH3_phase_1.png)


We can also check the distribution of orbital periods within the posterior


```python
res.plot_posterior_periods();
```

![png](GaiaBH3_files/GaiaBH3_periods_1.png)


## GAIAmodel with a known-object


We now set-up a run using the known-object mode, to give priors specifically for BH3, we chose priors wide enough to not bias the posterior constraints, but narrow enough that the sampling can be efficient. 

We then allow for additional Keplerians to be fit for up to a maximum number of N. This is then searching for additional periodicities in the data.

We now allow the jitter to be free rather than fixing to a specific value.

Here is an example functional setup that can be called to run the model


```python
def GAIA_example(run=False, load=False, **kwargs):
	# load the right data file, here is an example, though if running the example, you can also load the data directly from the example as above
	data = GAIAdata('Gaia_astrometry_BH3.gaia',skip=2,units='mas')
	data.M0_epoch =  57936.875 #Set the epoch to the relevant Gaia reference time (here in tcb - 2400000)
	# create the model
	model = GAIAmodel(fix=False, npmax=2, data=data)

	#We now let the Jitter be a free parameter
	model.Jprior = ModifiedLogUniform(0.01,0.5)

	#We could use the Thiele-Innes parametrisation, but instead chose to use the geometric elements. If Thiele-Innes used instead then priors cannot be set on a0 or cosi
	# model.conditional.thiele_innes = True
	# model.thiele_innes = True

	#Specify priors for the free search, here we let the priors on omega, Omega, and phi be default by not specifying them
	model.conditional.eprior = Kumaraswamy(0.867,3.03)
	model.conditional.Pprior = LogUniform(100.0,10000.0)
	model.conditional.aprior = ModifiedLogUniform(0.1,100.0)
	model.conditional.cosiprior = Uniform(-1,1)

	#Specify the priors for the 5-parameter astrometric solution, the values can be taken from the Gaia catalogue with appropriately wide priors to not bias the results
	model.da_prior = Gaussian(4.2,0.4)
	model.dd_prior = Gaussian(2.4,0.3)
	model.mua_prior = Gaussian(-30, 80)
	model.mud_prior = Gaussian(150, 80)
	model.parallax_prior = Uniform(1.3,1.9)

	#Specify the stars mass and RA and DEC, not needed for the analysis but can be useful for the handling of the posteriors and making plots
	model.star_mass = 1.0
	model.RA = 294.82796478104
	model.DEC = 14.931669719919999

	#We set the number of known objects to 1
	model.set_known_object(1)
	#We set the priors for the known-object mode, here there is 1 known object the black hole. We specify priors that will allow kima to sample efficiently, but not bias the results
	model.KO_Pprior = [Gaussian(4280,500)]
	model.KO_aprior = [Gaussian(27.3,4.0)]
	model.KO_eprior = [Gaussian(0.73,0.04)]
	model.KO_wprior = [Gaussian(1.36,0.05)]
	model.KO_Wprior = [Gaussian(2.38,0.05)]
	model.KO_phiprior = [Uniform(0,2*np.pi)]
	model.KO_cosiprior = [Gaussian(-0.35,0.05)]

	#Set the sampler parameters (change steps to set length of the run, change threads depending on machine, others are reasonable to leave)
	kwargs.setdefault('steps', 5000)
	kwargs.setdefault('num_threads', 4)
	kwargs.setdefault('num_particles', 2)
	kwargs.setdefault('new_level_interval', 20000)
	kwargs.setdefault('save_interval', 2000) 


	if run:
		kima.run(model, **kwargs)
	if load:
		res = kima.load_results(model)
		return model, res
		
	return model
```


Now run the model and load the results, the number of steps can be reduced to speed up at the cost of a less thoroughly explored posterior


```python
model, res = GAIA(run=True, load=True, steps=20000, progress=True)
```

    # Seeding random number generators. First seed = 1781060679.
    # Generating 8 particles from the prior...done.
    # Sampling...
    # Creating level 1 with log likelihood = -1305757414.83.
    # Creating level 2 with log likelihood = -282179412.547.
    # Creating level 3 with log likelihood = -141679422.529.
    # Creating level 4 with log likelihood = -86785778.6077.
    # Saving particle to disk. N = 50.
    # Creating level 5 with log likelihood = -57884951.3507.
    # Creating level 6 with log likelihood = -36261310.0672.
    # Saving particle to disk. N = 100.
    # Creating level 7 with log likelihood = -22691084.6512.
    # Creating level 8 with log likelihood = -12866737.3605.
    # Saving particle to disk. N = 150.
    [... output truncated ...]
    # Saving particle to disk. N = 19900.
    # Saving particle to disk. N = 19950.
    # Saving particle to disk. N = 20000.
    # Took 393.178 seconds
    log(Z) = 29.92
    Information = 64.81 nats
    Effective sample size = 3144.8



Plot the $Np$ histogram, we find that a model with no additional Keplerians is favoured


```python
res.plot_posterior_np();
```

    Np probability ratios:  [2.03639847 1.46660395]


![png](GaiaBH3_files/GaiaBH3_Nphist_2.png)


We can then take the maximum likelihood sample within $Np$=0 (recall the known-object is separate) and plot the astrometric phase-fold plot


```python
p = res.maximum_likelihood_sample(Np=0)
```

    Sample with the highest likelihood value  (logL = 101.45)
    from samples with Np=0 only
    -> might not be representative of the full posterior distribution

    jitter:
      [0.05633646]
    number of known objects:  1
    orbital parameters:           P         phi         ecc           a           w        cosi           W 
                         4235.84846     5.92650     0.72837    27.27586     1.35799    -0.35181
         2.37614



```python
res.phase_plot(p,dates='tcb',date_sub=2400000,include_jitter=True);
```

![png](GaiaBH3_files/GaiaBH3_phase_2.png)


Noticing that the uncertainties on the data look much better than when we had fixed the jitter, we can plot a histogram of the posterior constraint on the jitter


```python
res.hist_jitter();
```

![png](GaiaBH3_files/GaiaBH3_jitter_2.png)


We can also plot the orbit plot with a set of posterior samples to see the range of solutions compatible with the data. Note that the data shown is the residual with the baseline astrometry removed for the maximum-likelihood solution, so the other posteriors should not be directly compared to the data.


```python
res.plot_random_samples(ncurves=200,Np=0);
```

    Astrometry orbit plot depends on the subtraction of the paralactic motion, therefore the set of posterior samples shown will not be truly comparable to the data (which is shown with the maximum-likelihood paralactic solution removed)


![png](GaiaBH3_files/GaiaBH3_samples_2.png)


## The RVGAIA model


We can also use the radial velocity data (in this case from the Gaia RVS
instrument) in combination with the astrometric data to obtain better
constraints. We use the `RVGAIAmodel` which effectively combines the `GAIAmodel`
and the `RVmodel` classes for a joint analysis.


```python
def RVGAIA_example(run=False, load=False, **kwargs):
	#Load the right data files we now need to call an instance of GAIAdata to load in the astrometric data 
	#and one of RVData for the radial velocities. The reference epoch will be that of the astrometric data
	#we specify it explicitly.
	Gdata = GAIAdata('Gaia_astrometry_BH3.gaia',skip=2,units='mas')
	Vdata = RVData('Gaia_RVs_BH3.rdb',skip=2,units='kms')
	Gdata.M0_epoch =  57936.875
	# create the model giving both datatypes to the model
	model = RVGAIAmodel(fix=False, npmax=2, GAIAdata = Gdata, RVData=Vdata)

	#Set priors for systemic velocity and jitters for astrometry and RVs
	model.Cprior = Uniform(-400000,-300000)
	model.J_GAIA_prior = ModifiedLogUniform(0.01,0.2)
	model.J_RV_prior = ModifiedLogUniform(100.0,10000.0)

	#Set priors on planet parameters. Note that in the RVGAIAmodel we fit with a0 as the free parameter, and this is converted to K internally to calculate the RV Keplerian
	#here we let the priors on omega, Omega, and phi be default by not specifying them
	model.conditional.eprior = Kumaraswamy(0.867,3.03)
	model.conditional.Pprior = LogUniform(10.0,4000.0)
	model.conditional.aprior = ModifiedLogUniform(0.01,2)
	model.conditional.cosiprior = Uniform(-1,1)

	#Specify the priors for the 5-parameter astrometric solution, the values can be taken from the Gaia catalogue with appropriately wide priors to not bias the results
	model.da_prior = Gaussian(4.2,0.4)
	model.dd_prior = Gaussian(2.4,0.3)
	model.mua_prior = Gaussian(-0.08,0.2)
	model.mud_prior = Gaussian(-0.42,0.2)
	model.parallax_prior = Uniform(1.3,1.9)

	#Specify the stars mass and RA and DEC, not needed for the analysis but can be useful for the handling of the posteriors and making plots
	model.star_mass = 1.2
	model.RA = 294.82796478104
	model.DEC = 14.931669719919999


	#We set the number of known objects to 1
	model.set_known_object(1)
	#We set the priors for the known-object mode, here there is 1 known object the black hole. We specify priors that will allow kima to sample efficiently, but not bias the results	
	model.KO_Pprior = [Gaussian(4280,500)]
	model.KO_aprior = [Gaussian(27.3,4.0)]
	model.KO_eprior = [Gaussian(0.73,0.04)]
	model.KO_wprior = [Gaussian(1.36,0.05)]
	model.KO_Wprior = [Gaussian(2.38,0.05)]
	model.KO_phiprior = [Uniform(0,2*np.pi)]
	model.KO_cosiprior = [Gaussian(-0.35,0.05)]

	#Set the sampler parameters (change steps to set length of the run, change threads depending on machine, others are reasonable to leave)
	kwargs.setdefault('steps', 5000)
	kwargs.setdefault('num_threads', 4)
	kwargs.setdefault('num_particles', 2)
	kwargs.setdefault('new_level_interval', 20000)
	kwargs.setdefault('save_interval', 2000)


	if run:
		kima.run(model, **kwargs)
	if load:
		res = kima.load_results(model)
		return model, res
	return model
```


Now run the model and load the results, the number of steps can be reduced to speed up at the cost of a less well explored posterior


```python
model, res = RVGAIA(run=True, load=True, steps=60000)
```

    # Seeding random number generators. First seed = 1780740180.
    # Generating 8 particles from the prior...done.
    # Sampling...
    # Creating level 1 with log likelihood = -1157133890.21.
    # Creating level 2 with log likelihood = -265303747.347.
    # Creating level 3 with log likelihood = -139357008.117.
    # Creating level 4 with log likelihood = -87805646.6976.
    # Saving particle to disk. N = 50.
    # Creating level 5 with log likelihood = -56297994.858.
    # Creating level 6 with log likelihood = -36672355.5911.
    # Saving particle to disk. N = 100.
    # Creating level 7 with log likelihood = -22702424.9594.
    # Creating level 8 with log likelihood = -13540774.5233.
    # Saving particle to disk. N = 150.
    [... output truncated ...]
    # Saving particle to disk. N = 59900.
    # Saving particle to disk. N = 59950.
    # Saving particle to disk. N = 60000.
    # Took 2102.973 seconds
    log(Z) = -125.71
    Information = 70.13 nats
    Effective sample size = 10208.5



plot the $Np$ histogram, again no extra Keplerian is detected


```python
res.plot_posterior_np();
```

    Np probability ratios:  [0.98035547 1.22678117]


![png](GaiaBH3_files/GaiaBH3_Nphist_3.png)


Obtain the maximium-likelihood $Np=1$ sample and plot the phase-fold panels. This time there is a separate plot for the radial velocity data and the astrometric data.


```python
p = res.maximum_likelihood_sample(Np=0)
```

    Sample with the highest likelihood value  (logL = -48.83)
    from samples with Np=0 only
    -> might not be representative of the full posterior distribution

    jitter:
      [ 0.05500217 54.87612963]
    number of known objects:  1
    orbital parameters:           P         phi         ecc           a           w        cosi           W 
                         4454.58464     5.94738     0.73953    28.35130     1.35408    -0.34964
         2.38124
    vsys:  -357507.28517105



```python
res.phase_plot(p,dates='tcb',date_sub=2400000,include_jitter=True);
```

![png](GaiaBH3_files/GaiaBH3_phase_3.png)

![png](GaiaBH3_files/GaiaBH3_rvphase_3.png)


We can plot the histograms for the Jitters, and vsys 


```python
res.hist_jitter()
res.hist_vsys();
```

![png](GaiaBH3_files/GaiaBH3_jitter_3.png)

![png](GaiaBH3_files/GaiaBH3_vsys_3.png)


Let's plot the phase-fold for the maximum-likelihood $Np=1$ sample to see what the best possible other keplerian in this system would be. It is, as expected, simply a Keplerian within the noise.


```python
p = res.maximum_likelihood_sample(Np=1)
res.phase_plot(p,dates='tcb',date_sub=2400000,include_jitter=True);
```

    Sample with the highest likelihood value  (logL = -38.79)
    from samples with Np=1 only
    -> might not be representative of the full posterior distribution

    jitter:
      [5.35182100e-02 1.24309724e+02]
    number of planets:  1
    orbital parameters:           P         phi         ecc           a           w        cosi           W 
                          310.79493     5.87053     0.39556     0.05385     1.53523    -0.12742     5.02163
    number of known objects:  1
    orbital parameters:           P         phi         ecc           a           w        cosi           W 
                         4070.71296     5.91013     0.71799    26.44472     1.35872    -0.35176
         2.37227
    vsys:  -356473.67997901


![png](GaiaBH3_files/GaiaBH3_phase_3_2.png)

![png](GaiaBH3_files/GaiaBH3_rvphase_3_2.png)
