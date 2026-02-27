# autostructureLevelReader

The python code hosted here will read the standard oic (... and olg) files of [`autostructure`](https://amdpp.phys.strath.ac.uk/autos/). At present the code will only reliably read outputs from intermediate coupling from an `oic` file. There is limited support for reading the `TERMS` output in an LS case.  I will eventually add support for configuration-average. If you ran `autostructure` with `mprint=5`, then this code can also read the corresponding eigenvector outputs from the file `olg`.

The success in reading the formatted output `autostructure` is conditional on your structure not overflowing the output capacity. There are two important format strings within `autostructure` that may be edited to circumvent this. This is not needed for the running of this code for small runs, but if you have a large run it may become necessary.

Running `python3 /path/to/as_ls_table.py -h` will show the command line options available to the user. 

