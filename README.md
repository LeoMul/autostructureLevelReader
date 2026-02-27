# autostructureLevelReader

The python code hosted here will read the standard oic (... and olg) files of [`autostructure`](https://amdpp.phys.strath.ac.uk/autos/). At present the code will only reliably read outputs from intermediate coupling from an `oic` file. There is limited support for reading the `TERMS` output in an LS case.  I will eventually add support for configuration-average. If you ran `autostructure` with `mprint=5`, then this code can also read the corresponding eigenvector outputs from the file `olg`. At present the code does not support continuua states.

A tool like this is (opinions may vary) essential for spectral identification. Its only requirement is numpy.

The success in reading the formatted output `autostructure` is conditional on your structure not overflowing the output capacity of that code. There are two important format strings within `autostructure` that may be edited to circumvent this. This is not needed for the running of this code for small runs, but if you have a large run it may become necessary.

Running `python3 /path/to/as_ls_table.py -h` will show the command line options available to the user. 

The main ones are as follows, 

***Required Input***:
\
`-l /path/to/oic` path to `oic` file.

`-t /path/to/TERMS` path to `TERMS` file. Cannot be used in conjunction with an oic file. Note this does not look for an `ols` file, for historical reasons.

***Output Formatting***:
\
`-c num` will force the code to only output occupations from the `num+1`'th orbital onwards. This is perhaps useful for not showing all of the core occupations.

`-n num` will force the code to only output `num` levels. 

`-v` will look for and read the file `olg` looking for the CI-matrix. There is not a lot of strict checking in the corresponding routine, use with care and make sure you had `mprint=5`. 

`-u num` will choose the `num`'th energy unit: 0 = Ryd; 1 = eV; 2 = cm$^{-1}$. Anything else will be changed to 0.


***Structure Diagnosing***:
\
`-g num` will change the level output such that the `num`'th level has zero energy. Useful for diagnosing unconverged structures. Note that `num` is zero-indexed here, so `-g 1` corresponds to making the first excited state (level 2) the ground.


There also routines in `as_lib.py` for A-value reading, this is a work in progress. 

***Example run***: 
\
Consider the `autostructure` run with `das` file,
```
A.S.
&SALGEB CUP='ICR' MXVORB=4 MXCONF=3  MPRINT=5 KCOR1=1 KCOR2=1 &END
  2  0  2  1  3  0  3  1  
     2     4     0     0  
     2     3     1     0  
     2     3     0     1  
&SMINIM NZION= 26 &END

```
Running `autostructure`, and then `python3 /path/to/as_ls_table.py -l oic > ls.dat`  will output into `ls.dat`
```
Index,  Energy( Ryd ),                      CSF(TERM),     J,     LV
    1,     0.00000000,      2s2    2p4          (3P ),   2.0,     15
    2,     0.70993300,      2s2    2p4          (3P ),   0.0,     37
    3,     0.77961300,      2s2    2p4          (3P ),   1.0,     26
    4,     1.54034200,      2s2    2p4          (1D ),   2.0,     11
    5,     3.18021700,      2s2    2p4          (1S ),   0.0,     41
    6,    60.91990400,      2s2    2p3    3s1   (5S*),   2.0,     22
    7,    61.40898500,      2s2    2p3    3s1   (3S*),   1.0,     36
    8,    62.33950400,      2s2    2p3    3s1   (3D*),   2.0,     19
    9,    62.34278200,      2s2    2p3    3s1   (3D*),   1.0,     33
   10,    62.60669800,      2s2    2p3    3s1   (3D*),   3.0,     7
   11,    62.79437500,      2s2    2p3    3s1   (1D*),   2.0,     20
   12,    63.05794800,      2s2    2p3    3p1   (5P ),   1.0,     25
   ...
``` 
Similarly, since  `MPRINT=5` was on, we can run `python3 /path/to/as_ls_table.py -l oic -v > ls_vec.dat` to get,
``` 
Index,  Energy( Ryd ),    Jp,    LV,     T,   Eigenvector
     1,    0.00000000,  2.0e,    15,    11,   90.52% [ 2s2    2p4          (3P ) ] -  9.37% [ 2s2    2p4          (1D ) ] 
     2,    0.70993300,  0.0e,    37,    11,   84.22% [ 2s2    2p4          (3P ) ] + 15.67% [ 2s2    2p4          (1S ) ] 
     3,    0.77961300,  1.0e,    26,    11,   99.88% [ 2s2    2p4          (3P ) ] 
     4,    1.54034200,  2.0e,    11,     6,   90.52% [ 2s2    2p4          (1D ) ] +  9.37% [ 2s2    2p4          (3P ) ] 
     5,    3.18021700,  0.0e,    41,    22,   84.25% [ 2s2    2p4          (1S ) ] - 15.66% [ 2s2    2p4          (3P ) ] 
     6,   60.91990400,  2.0o,    22,    19,   92.03% [ 2s2    2p3    3s1   (5S*) ] -  6.87% [ 2s2    2p3    3s1   (3P*) ] +  0.73% [ 2s2    2p3    3s1   (3D*) ] 
     7,   61.40898500,  1.0o,    36,    21,   86.43% [ 2s2    2p3    3s1   (3S*) ] -  6.15% [ 2s2    2p3    3s1   (1P*) ] -  3.89% [ 2s2    2p3    3s1   (3P*) ] +  3.52% [ 2s2    2p3    3s1   (3D*) ] 
     8,   62.33950400,  2.0o,    19,     5,   68.28% [ 2s2    2p3    3s1   (3D*) ] - 16.10% [ 2s2    2p3    3s1   (3P*) ] - 11.44% [ 2s2    2p3    3s1   (1D*) ] -  4.19% [ 2s2    2p3    3s1   (5S*) ] 
     9,   62.34278200,  1.0o,    33,     5,   80.16% [ 2s2    2p3    3s1   (3D*) ] -  8.67% [ 2s2    2p3    3s1   (3S*) ] -  6.39% [ 2s2    2p3    3s1   (1P*) ] -  4.79% [ 2s2    2p3    3s1   (3P*) ] 
    10,   62.60669800,  3.0o,     7,     5,  100.00% [ 2s2    2p3    3s1   (3D*) ] 
    11,   62.79437500,  2.0o,    20,     9,   77.25% [ 2s2    2p3    3s1   (1D*) ] + 20.08% [ 2s2    2p3    3s1   (3D*) ] +  2.34% [ 2s2    2p3    3s1   (3P*) ] 
    12,   63.05794800,  1.0e,    25,    10,   88.13% [ 2s2    2p3    3p1   (5P ) ] +  3.40% [ 2s2    2p3    3p1   (3P ) ] -  3.08% [ 2s2    2p3    3p1   (3S ) ] +  3.06% [ 2s2    2p3    3p1   (3P ) ] 
    ...
``` 
where now the eigenvectors are being output as well.