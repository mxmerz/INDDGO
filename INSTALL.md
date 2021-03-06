## Pre-requisites for INDDGO:

- For parallel versions:
        - an MPI compiler (tested with OpenMPI and Cray MPI)
        - a C++ compiler capable of compiling OpenMP code (tested with GCC >= 4.4.3)

- For building versions with MADNESS:
        - autoconf
        - automake

## Configuration:

To configure INDDGO, a make.inc file must be created, containing
the various options for compilers and optional software packages.

We provide two example files, make.inc.parallel and make.inc.serial
to get you started.  Simply copy one of these files to make.inc.
Next, edit it and set the `SRC_DIR` variable to the directory where
you unpacked INDDGO. 

Various `HAS_<PACKAGENAME>` variables may be set.  If set, you must also
set the corresponding directory variable further on in the `make.inc`.

Note that trailing spaces in your variable declarations may cause 
Make to fail in seemingly random ways (directories and `0`/`1` values are 
particularly susceptible).

We provide a copy of the MADNESS runtime library, however other
dependencies must be installed manually.

For details on where to obtain optional dependencies and their required
versions, see the `thirdparty.txt` file.

## Compilation:

To compile INDDGO, follow these steps:

### make

Resulting binaries will be places in `SRC_DIR/bin`.  INDDGO also provides
various non-default binaries that may be built for testing purposes.
These will be placed in the appropriate sub-package's `/bin` directory.

The `max_wis/bin` directory contains the `serial_wis` binary which is the 
most well-tested. It can be used to solve WIS instances and also do
general purpose tree decomposition-related tasks. See the `README`
in the `max_wis` directory for more details.

Tested with the GNU compilers (g++, gcc, version >= 4.4.3) and OpenMPI. 
No support is provided for non-GCC compilers.

### Mac users: 

Tested with default `xcode gcc 4.2.1`. You need to set the `OLDGCC` variable
in your `make.inc` file.
**WARNING:** Some MADNESS developers have reported problems
with the new Lion (10.7) MacOS version causing runtime errors, including 
segmentation faults and incorrect results in upper-level MADNESS 
functionality. Since INDDGO's parallel dynamic programming relies on 
the MADNESS runtime, similar problems may occur (we have not done extensive 
testing). One possible solution is to compile for Snow Leopard (10.6), 
which can be done by setting an environment variable. 

### Example

Here are details on how to obtain Metis and SuiteSparse, compile them, and
instruct INDDGO to use these additional capabilities. The links are current
as of 2016-07-22:

The top level directory for this example is `/home/cgroer`.
It will contain INDDGO, Metis, and SuiteSparse.

``` bash
cd /home/cgroer

mkdir INDDGO 
git clone https://github.com/bdsullivan/INDDGO.git

wget http://glaros.dtc.umn.edu/gkhome/fetch/sw/metis/metis-5.0.2.tar.gz
tar xzvf metis-5.0.2.tar.gz
cd metis-5.0.2
make config
make
# OPTIONAL: make install
cd ..

wget http://faculty.cse.tamu.edu/davis/SuiteSparse/SuiteSparse-4.0.2.tar.gz
tar xzvf SuiteSparse-4.0.2.tar.gz
cd SuiteSparse
make
# OPTIONAL: make install

cd ..
cd INDDGO
```

Edit `make.inc`, example edit below:

```
SRC_DIR = /home/cgroer/INDDGO
...
HAS_SUITESPARSE = 1
...
HAS_METIS       = 1
...
SUITESPARSE = /home/cgroer/SuiteSparse
...
METIS_SRC_DIR = /home/cgroer/metis-5.0.2
METIS_LIB_DIR = /home/cgroer/metis-5.0.2/build/Linux-x86_64/libmetis
````

Done editing `make.inc`. Now ready to build INDDGO:

```
> make
> cd bin
```

This should be it.  Do a quick test:

```
> ./serial_wis.exe -f ../sample_graphs/1dc.64.dimacs -metmmd -gavril
file n m w obj
../sample_graphs/1dc.64.dimacs 64 543 35 10
> ./serial_wis -f ../sample_graphs/1dc.64.dimacs -amd -gavril
file n m w obj
../sample_graphs/1dc.64.dimacs 64 543 32 10
```
