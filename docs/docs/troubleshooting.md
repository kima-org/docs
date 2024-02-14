<!-- <style>
.md-nav--secondary {
  display: none;
}
</style> -->


### Problems with installation

- Make sure you cloned the repository with `--recursive`, to get the submodules
- Make sure the version of gcc you are using accepts the option `-std=c++11`.  
  Feel free to edit the Makefile and change the `CXX` variable

- If you can't get it to work, [open an issue](https://github.com/kima-org/kima/issues) 
  on GitHub and let's try to fix it.


### Other issues

- Segmentation faults  
  If you run into a segmentation fault before **kima** even prints anything to
  the terminal, it might be due to the header of the data files. When reading
  the data, make sure the `skip` (the third) parameter to the `load` or
  `load_multi` functions is set correctly. That is, make sure it matches the
  number of lines in the header of the file(s). If using `load_multi`, also make
  sure the headers occupy the same number of lines in all files.
  

### I think something is wrong...

Let us know in the [issues](https://github.com/kima-org/kima/issues) if you think
there is a problem or if something doesn't work as expected.


### I could do with some help analysing RVs

**kima** is free software and provided *as is* within the terms of the
[LICENSE](https://github.com/j-faria/kima/blob/master/LICENSE). However, we are
available to help if we can! The [issue
tracker](https://github.com/j-faria/kima/issues) is a good place to ask
questions about **kima** or the analysis of RV data, but feel free to contact us
by email if needed.




***
