<style>
.md-nav--secondary {
  display: none;
}
</style>


# Welcome

These pages hold the documentation for **kima**.  
Follow the links on the left for the different sections.
A number of examples on how to use the code are also available. These are
executed every time the code changes, so they're up-to-date.


### Authors and License

Written by [João Faria](https://joaofaria.space) 
and [contributors](https://github.com/j-faria/kima/graphs/contributors),
building on original code by [Brendon Brewer](https://brendonbrewer.com/).  
Licensed under the MIT license 
(see the [LICENSE](https://github.com/j-faria/kima/blob/master/LICENSE)).

---


Read more [about **kima**](/about) or see the [news](/news).


??? tip "I just want to find planets!"

    If you’re really busy, do this

    ```sh
    git clone --recursive https://github.com/j-faria/kima.git  #(1)

    cd kima
    make -j 4
    python setup.py install

    cd examples/*CHOOSE ONE*

    kima-run
    kima-showresults all
    ```

    1.  Don't forget the `--recursive`!

