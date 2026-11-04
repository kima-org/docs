---
title: The label-switching degeneracy
date: 2026
authors:
  - name: Faria, J. P.
    orcid: 0000-0002-6728-244X

filled: true
---

# The label-switching degeneracy


<div markdown="1" class="no-print">

!!! quote "Cite as"

    Faria, J. P. [:fontawesome-brands-orcid:{ .orcid }](https://orcid.org/0000-0002-6728-244X)
    (2026). *The label-switching degeneracy*
    (kima Research Notes). Zenodo.  
    DOI: [10.5281/zenodo.23962949](https://doi.org/10.5281/zenodo.23962949){:target="_blank"}  
    License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/){:target="_blank"}


    If you use **kima**, please also cite
    [Faria et al. (2018)](https://ui.adsabs.harvard.edu/abs/2018JOSS....3..487F){:target="_blank"}.

    ??? note "BibTeX"

        ```bibtex
        @misc{faria2026labelswitching,
          author    = {Faria, J.~P.},
          title     = {The label-switching degeneracy},
          year      = {2026},
          publisher = {Zenodo},
          doi       = {10.5281/zenodo.23962949},
          url       = {https://doi.org/10.5281/zenodo.23962949}
        }
        ```

</div>

<p class="print-only">
<strong>Author:</strong> J. P. Faria
</br>
<strong>License:</strong>
<a href="https://creativecommons.org/licenses/by/4.0/" target="_blank">CC BY 4.0</a>
</p>


## Introduction

In the models used in **kima**, the number of Keplerian signals $N_p$ can be a
free parameter, and the posterior samples can therefore contain different
numbers of planets. The planets in a given sample are stored in a fixed order,
as `P0, P1, ...`, `K0, K1, ...`, and so on, so it is natural to read the first
column of orbital periods as "the period of planet b", the second as "the period
of planet c", etc. This note explains why this reading is not always justified
and why this is a natural property of the posterior. It also describes the tools
implemented in **kima** to deal with this degeneracy: a function to *re-label*
the posterior samples, and a helpful visualization that can help diagnose the
problem.

The effect is well known in the Bayesian analysis of mixture models, where it
is called *label switching* (e.g. Stephens 2000[^1]; Jasra, Holmes & Stephens
2005[^2]), and it is common to any model whose components are exchangeable.


## Exchangeable planets

Consider a model with $k$ Keplerian signals, each described by a parameter
vector 
$$
\theta_j = (P_j, K_j, e_j, \omega_j, \phi_j), \qquad \text{for}\,\, j = 1, \dots, k
$$

The RV model is the sum of the individual Keplerian signals,
$$
v(t) = \sum_{j=1}^{k} \kappa(t;\, \theta_j),
$$

and the order of the terms in the sum is irrelevant. Therefore, the likelihood
is invariant under any permutation $\sigma$ of the labels,
$$
\mathcal{L}(d \mid \theta_{\sigma(1)}, \dots, \theta_{\sigma(k)}) =
\mathcal{L}(d \mid \theta_1, \dots, \theta_k).
$$

When all planets share the same prior, $\pi(\theta_1, \dots, \theta_k \mid k) =
\prod_j \pi(\theta_j)$, the prior is also invariant under permutations, and so
is the posterior:
$$
p(\theta_{\sigma(1)}, \dots, \theta_{\sigma(k)} \mid d, k) =
p(\theta_1, \dots, \theta_k \mid d, k).
$$

This means that if the posterior has a mode with the planets in a given
arrangement, it will actually have $k!$ identical copies of that mode, one for
each way of assigning the labels. These copies are not different physical
solutions: they describe exactly the same planetary system.

!!! note "This is not a bug"

    The label-switching degeneracy is an intrinsic property of the posterior
    distribution, not an error in the sampler or in the model. In fact,
    a sampler that explores the posterior *correctly* will visit all the
    equivalent modes, and it is precisely when it does that the problem
    becomes visible. If the samples are correct draws from the posterior 
    distribution, some mixing in the planet labels is expected.


Suppose a system has two well-separated signals. If the labels switched during
sampling, the column `P0` will contain a mixture of samples from both signals,
and its marginal distribution will be bimodal. Summaries such as the median, the
mean, or credible intervals of `P0` are then meaningless (the median, for
example, may fall somewhere between the two periods). The same applies to every
parameter of the affected planets, such as amplitude and eccentricity. The
effect may be more or less visible in different plots or summaries of the
posterior (e.g. histograms and corner plots).

For trans-dimensional models like those used in **kima**, the situation is a bit
more subtle, because samples with different values of $N_p$ are mixed in the
same results table. A planet that shows up in the first column in one sample
with $N_p = 1$ can appear in the second column in another sample with $N_p = 2$,
as shown below

```txt title="posterior_sample.txt"
...  Np  P0  P1  ...
     ...
...  1   24  0   ...
...  2   38  24  ...
     ...
```


!!! info "Label switching versus genuine multimodality"

    Label switching should not be confused with genuine multimodality of the
    posterior, for example due to aliasing of the orbital period. Alias peaks
    are *physically distinct* solutions with different (even if similar) 
    likelihoods, and they remain if the labels were fixed. The copies produced 
    by label switching all have exactly the same likelihood.


Label switching is not always a problem. Any quantity which is invariant under
permutations of the planet labels is unaffected. For example, the posterior for
$N_p$, the total RV model (that is, the predictive distribution), or the
probability that a system has *at least one* planet in a given region of the
period-mass plane, should not be affected by label switching. Re-labelling is
only needed when one wants to study the properties of *individual* signals (or
planets).


## A simple simulated example

To illustrate the problem, let's consider simulated RV data for a system with
two planets:

|                 | planet *b* | planet *c* | 
|                 |------------|------------|
| period          | 24 days | 38 days |
| semi-amplitude  | 0.9 m/s  | 1.7 m/s  |
| eccentricity    | 0.2      | 0.0      |

observed at 57 random epochs:

<figure markdown="span">

![data](../figures/label-switching-plot_data.png){ width="80%" }

</figure>

We will analyse these data with **kima** with a very simple model containing up
to three Keplerians, assuming all the default priors:

```python
from kima import RVData, RVmodel

data = RVData(...)

model = RVmodel(fix=False, npmax=3, data=data)
```

and run the sampler for a few thousand steps:

```python
kima.run(model, steps=20_000)
```

The analysis below uses the results of this run obtained with

```python
res = kima.load_results(model)
```


## Visualizing the problem

In order to visualize the label-switching degeneracy, parameter histograms may
be of limited use since they only show the marginal distributions and thus may
just show bimodality. Labelling the posterior samples in joint plots is more
helpful:

`res.plot_posterior_periods()` |  `res.plot_posterior_PKE()`
:-------------------------:|:-------------------------:
![plot2](../figures/label-switching-plot2.png) | ![plot3](../figures/label-switching-plot3.png)


The plot on the right shows the mixing of different colours in the two main
posterior peaks corresponding to the properties of the two planets. The
posterior for the third Keplerian signal is mostly unconstrained revealing the
lack of a significant third signal. This is also visible in the posterior for
$N_p$, which points to the significant detection of two planets:

<figure markdown="span">

![plot1](../figures/label-switching-plot1.png){ width="80%" }

</figure>

A more useful visualization of the labelling can be a **parallel coordinates
plot**. In this type of plot, each parameter is represented by a vertical axis,
and each posterior sample is drawn as a line that crosses all the axes at the
values of that sample. This is implemented in the *pykima* function
`pykima.display.label_switching_plot`, which produces a figure like the
following:

![fig1](../figures/label-switching-1.png)

In this figure, the semi-amplitudes and periods of all planets in all posterior
samples are concatenated and coloured by their planet label (that is, the column
in which they appear in the posterior table). Label switching becomes apparent
when lines of different colours cross in between the axes. 


## Re-labelling the posterior samples

A standard solution to label switching is to impose an identifiability
constraint during or after the sampling, by re-ordering the planets in each
posterior sample in a consistent way. A simple constraint, such as ordering
(sorting) each sample by increasing period, often works for well-separated
signals, but breaks down as soon as the posterior for two periods overlaps, and
it ignores the other parameters. In our example above, such an ordering would
also not correctly account for the third Keplerian present in *some* of the
posterior samples.

An alternative re-labelling approach implemented in **kima** instead uses a
reference solution, the sample with the maximum likelihood, and matches the
planets of every other sample to the planets of that reference, based on a
measure of distance.

### Notation

Let $N_{p,\max}$ be the maximum number of planets allowed in the model (3 in our
example). Any posterior sample $s$ has the planet parameters stored in slots $m
= 1, \dots, N_{p, \max}$, each with orbital period $P^{(s)}_m$ and
semi-amplitude $K^{(s)}_m$, plus the remaining parameters of the planet. The
unused slots (when there are fewer than $N_{p, \max}$ planets in a sample) are
represented by zeros for all the parameters. The reference sample has periods
and semi-amplitudes $P^\star_j$ and $K^\star_j$.

### Algorithm

1. **Reference.** Take the maximum-likelihood sample as reference and order its
   planets by decreasing $K$ (the default), so that slot $j=1$ corresponds to
   the strongest signal. Alternatively, the reference planets can be ordered by
   increasing period.

2. **Number of slots to re-label.** Only the first $J$ slots are processed,
   where $J$ is the number of *detected* planets, according to a Bayes-factor
   threshold on the posterior distribution of $N_p$. Alternatively, all slots
   but the last one are processed ($J = N_{p,\max} - 1$). Slots beyond $J$ are
   not matched to anything, as the corresponding "planets" are not expected to
   be well-localized in the posterior.

3. **Scale.** Estimate the width of the posterior peak for each slot $j$ as
   the standard deviations $\sigma_{P,j}$ and $\sigma_{K,j}$ of the $P$ and $K$
   columns, using only the posterior samples with the highest likelihoods (those
   above the 84th percentile of the log-likelihood, i.e. the top 16%).

4. **Slot-by-slot matching.** For $j = 1, \dots, J$, and for every posterior
   sample, consider the planets still available, i.e. those in slots $m = j,
   \dots, N_{p,\max}$ (the slots before $j$ have already been assigned). Compute
   the distance between each of them and the $j$-th reference planet,

    $$
    d_j(m) = \sqrt{
      \left(\frac{P^{(s)}_m - P^\star_j}{\sigma_{P,j}}\right)^2 +
      \left(\frac{K^{(s)}_m - K^\star_j}{\sigma_{K,j}}\right)^2
    },
    $$

    and select the planet $m^\ast = \arg\min_m d_j(m)$. This planet is moved to
    slot $j$, along with *all* of its parameters, and the other planets keep
    their relative order in the remaining slots.

5. **Iteration.** If requested, repeat steps 3 and 4 a number of times, each
   time re-estimating the scales from the already re-labelled samples. The
   scales in the first iteration are inflated by the label switching itself (a
   column containing a mixture of signals is wider than a single signal), so
   further iterations refine them.


### Remarks

- The distance uses only the periods $P$ and semi-amplitudes $K$, each
  normalized by the width of the posterior peak of that slot. This distance is
  therefore dimensionless and roughly measures "how many standard deviations" a
  candidate planet is from the reference sample. The other parameters do not
  enter the distance but are permuted together with $P$ and $K$.

- The slots are processed sequentially, and so the order of the slots matters:
  with the default ordering by decreasing $K$, the strongest reference signal
  gets the first spot.

- Only the standard deviations of $P$ and $K$ are used (the correlation between
  them is ignored), and only for the highest-likelihood samples.

### Implementation

This re-labelling algorithm is implemented in the *pykima* function
`pykima.analysis.relabel_posterior`, which takes a results object `res` as
input, and has the following options:

| Argument | Default | Meaning |
| --- | --- | --- |
| `replace` | `False` | If `True`, replace `res.posteriors` with the re-labelled version. The original array `res.posterior_sample` is **not** changed. The function always returns the re-labelled posteriors. |
| `iterations` | `1` | Number of iterations (steps 3 and 4). |
| `until_detected` | `True` | Re-label only the first $J$ slots, with $J$ the number of detected planets. Otherwise, $J = N_{\max} - 1$. |
| `sort_maximum_likelihood_by_K` | `True` | Order the planets of the reference sample by decreasing $K$. |
| `sort_maximum_likelihood_by_P` | `False` | Order the planets of the reference sample by increasing $P$. If both options are set, this one is applied last and takes precedence. |



### Limitations

- The result of this re-labelling depends on the reference sample. If the
  maximum-likelihood solution is not representative of the main posterior mode
  (e.g. it has more or fewer planets than the bulk of the samples, or it sits in
  a secondary peak), the labels will follow that choice.

- If two signals are close in the parameter space used for the distance (e.g.
  similar periods), the assignment can be ambiguous, and the re-labelled
  posteriors of the corresponding planets will remain partially mixed.

- Re-labelling is a post-processing step. It does not change the posterior or
  the model evidence, only the way in which the samples are presented.


### Results

After re-labelling the posterior samples, we can pass the new posterior arrays
to the parallel coordinates plot:

```python
post = kima.pykima.analysis.relabel_posterior(res, iterations=3)
kima.pykima.display.label_switching_plot(post)
```

resulting in the following figure:

![fig2](../figures/label-switching-2.png)

Note how the "first" planet has the largest semi-amplitude, since the default
ordering by decreasing $K$ has been applied to the reference sample.

## Summary

- The order of planets in the **kima** posterior samples is not meaningful by
  default. This is an intrinsic property of the posterior due to the
  exchangeability of the Keplerian signals.
- This *label switching* can invalidate summaries of individual planet
  properties (medians, intervals, histograms) but it does not affect
  permutation-invariant quantities such as the model evidence or posterior for
  $N_p$.
- **kima** provides a function to re-label the samples relative to the
  maximum-likelihood solution, and a parallel coordinates plot to diagnose the
  problem and check the result.


## Acknowledgements

The parallel coordinates plot implementation was adapted from the code available
[here](https://github.com/lindanickel/pcp).


## References

[^1]: Stephens, M. *Dealing with label switching in mixture models*. 
      Journal of the Royal Statistical Society: Series B (Statistical Methodology), 62, 795 (2000)  
      [DOI](https://doi.org/10.1111/1467-9868.00265)

[^2]: Jasra, A., Holmes, C. C., & Stephens, D. A. *Markov Chain Monte Carlo
      Methods and the Label Switching Problem in Bayesian Mixture Modeling*.
      Statistical Science, 20, 50 (2005)  
      [DOI](https://doi.org/10.1214/088342305000000016)
