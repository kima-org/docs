# Contributing to **kima**

If you are interested in contributing to the development of **kima**, thank you!
:pray:  
Feedback, bug reports, help with documentation, or code contributions are very
welcome. 

### Issues and Feature Requests

If you find a bug or run into some unexpected behaviour, feel free to [open an
issue on GitHub](https://github.com/kima-org/kima/issues){:target="_blank"}.

Sometimes, your specific analysis and setup will be hard to reproduce, so if you
can provide a minimal example, or representative data where the problem occurs,
it really helps! Feel free to also include a saved version of the model in a
`pickle` file, for example.




### Writing documentation

**kima**, like other software created _by_ researchers and _for_ research,
suffers from a lack of up-to-date comprehensive documentation. This is a shame,
but it's almost unavoidable. First, we are not software engineers, and therefore
lack the skills and knowledge necessary to produce and document the code at a
professional level. Second, none of the incentives in our professional life as
an astronomer align with writing documentation, because the system forces a
mindless focus on writing papers and securing the next position. (rant over :speaking_head:)

If you find an undocumented piece of code, or a mistake in the documentation,
please [open an
issue](https://github.com/kima-org/kima/issues){:target="_blank"} or [create a
pull request](https://github.com/kima-org/kima/pulls){:target="_blank"} to
correct it.


### Adding a new feature

If **kima** doesn't have the feature you need, feel free to propose an
implementation. For example, the [BINARIESmodel][kima.BINARIESmodel] and the
[ExponentialRayleighMixture][kima.distributions.ExponentialRayleighMixture]
distribution, among other things, were added by the community (see [this
paper](https://ui.adsabs.harvard.edu/abs/2023MNRAS.521.1871B/abstract) and [this
paper](https://ui.adsabs.harvard.edu/abs/2025MNRAS.539..727S/abstract); thank
you Tom and Adam!). 

Please don't be scared by the C++ code. You can probably go a long way just by
mimicking one of the existing models or data types.