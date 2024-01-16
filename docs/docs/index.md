---
hide:
  - toc 
---


**kima** is the name of a really good [drink produced in
Azores](https://azorieden.com/kima-a-drink-everyone-needs-to-try/)
:tropical_drink:  
And now, it's also a package for the analysis of radial velocity (RV) data.


<figure markdown>
  ![Image title](https://github.com/j-faria/kima/raw/master/paper/joss_figure.png){ width="800px" }
  <!-- <figcaption>Image caption</figcaption> -->
</figure>



In brief, **kima** fits Keplerian curves to a set of RV measurements, using the
Diffusive Nested Sampling algorithm[^1] to sample the posterior distribution for
the model parameters. Additionally, the code can calculate the fully
marginalized likelihood (or evidence, $Z$) of a model with a given number of
Keplerians and also *infer* the number ($N_p$) of Keplerian signals detected in
a given dataset. In this case, $N_p$ is a *free* parameter and its posterior
distribution is estimated[^2]<sup>, </sup>[^3].


The code is under active development at 
[this](https://www.github.com/kima-org/kima) :material-github: GitHub repository.  
This page hosts the documentation, [examples](/docs/examples), and detailed [APIs](/docs/API).


If you use **kima** in your work or research, we kindly ask that you include
the following citation[^4]

    Faria et al., (2018). kima: Exoplanet detection in radial velocities.
    Journal of Open Source Software, 3(26), 487

Feel free to use the following BibTeX entry, or see the ADS entry
[2018JOSS....3..487F](https://ui.adsabs.harvard.edu/abs/2018JOSS....3..487F/abstract).

```
@article{kima,
      title = {kima: Exoplanet detection in radial velocities},
      author = {Faria, J. P. and Santos, N. C. and Figueira, P. and Brewer, B. J.},
      journal = {Journal of Open Source Software},
      issn = {2475-9066},
      number = {26},
      volume = {3},
      pages = {487},
      date = {2018-06-19},
      year = {2018},
      month = {6},
      day = {19},
      publisher = {The Open Journal},
      doi = {10.21105/joss.00487},
      url = {http://dx.doi.org/10.21105/joss.00487},
}
```




#### References

[^1]: Brewer, B.J., Pártay, L.B. & Csányi, G. *Diffusive Nested Sampling*. Stat 
      Comput 21, 649–656 (2011)  
      [arXiv](https://arxiv.org/abs/0912.2380),
      [DOI](https://doi.org/10.1007/s11222-010-9198-8)

[^2]: Brewer, B.J., *Inference for Trans-dimensional Bayesian Models with
      Diffusive Nested Sampling* (2015)  
      [arXiv](https://arxiv.org/abs/1411.3921)

[^3]: Brewer, B.J. and Donovan, C.P., *Fast Bayesian inference for exoplanet
      discovery in radial velocity data*, MNRAS 448, 4, 3206–3214 (2015)  
      [arXiv](https://arxiv.org/abs/1501.06952),
      [DOI](https://doi.org/10.1093/mnras/stv199)

[^4]: Faria et al., *kima: Exoplanet detection in radial velocities*. Journal of
      Open Source Software, 3(26), 487 (2018)  
      [arXiv](https://arxiv.org/abs/1806.08305),
      [DOI](http://dx.doi.org/10.21105/joss.00487)
