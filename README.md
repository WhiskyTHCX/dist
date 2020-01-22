WhiskyTHC
=========

The Templated Hydrodynamics Code

WhiskyTHC is a state-of-the-art GRHD solver for Cactus.

Disclaimer
----------

WhiskyTHC is free software: you can redistribute it and/or modify it under the
terms of the GNU General Public License as published by the Free Software
Foundation, either version 3 of the License, or (at your option) any later
version.

WhiskyTHC is distributed in the hope that it will be useful, but WITHOUT ANY
WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE.  See the GNU General Public License for more details.

By using WhiskyTHC you agree that YOU WILL BE FULLY RESPONSIBLE FOR THE
CORRECTNESS OF ANY SCIENTIFIC RESULTS OBTAINED WITH THE CODE.

Downloading
-----------

This tarball contains scripts and microphysics tables for WhiskyTHC. It does
not contain the actual source code, which is instead hosted on
[bitbucket](https://bitbucket.org).

Use the GetComponents utility to checkout Cactus and WhiskyTHC

~~~
    $ ./scripts/GetComponents --parallel thornlists/full.th
~~~

This will download [Cactus](http://cactuscode.org/),
[Carpet](http://carpetcode.org/), [CTGamma](https://bitbucket.org/llamacode/),
and [WhiskyTHC](http://www.astro.princeton.edu/~dradice/whiskythc.html) and
place them in a folder called `Cactus`. This will also download
[batchtools](http://bitbucket.org/dradice/batchtools), the set of scripts I use
to manage simulations. 

[//]: # (GetComponents has some problem with the capitalization of repository names from)
[//]: # (bitbucket, where WhiskyTHC is hosted. To workaround these issues, it is)
[//]: # (necessary to run the `fixsymlinks.sh` script after `GetComponents`.)
[//]: # ()
[//]: # (~~~)
[//]: # (    $ ./scripts/fixsymlinks.sh)
[//]: # (~~~)

NOTE: `GetComponents` will place a copy of the thornlist used for the checkout
in `Cactus/thornlists`.

Compiling
---------

Detailed instructions on how to compile can be found in
`Cactus/doc/UsersGuide.pdf`. In brief, you need to load all necessary modules,
create a configuration, and then compile it.

Example of module lists for some of the machines I have compiled and run
WhiskyTHC can be found in the `Cactus/batchtools/templates/cactus` folder. If
you are using `bash` as login shell, you can load them with the `source`
command (in the `Cactus` folder):

~~~
    $ source batchtools/templates/cactus/comet.env
~~~

To create a configuration you will need to prepare a thornlist, that is a list
of components to be built, and a config file. An example of thornlist is the
`full.th` file, which contains everything I typically use in a production run.
Example of optionlists for some machines where I compiled and run WhiskyTHC can
be found in the `batchtools/templates/cactus` subfolder of Cactus.

For example, on SDSC Comet you can create a configuration 

~~~
    $ yes | make whiskythc THORNLIST=thornlists/full.th \
                           options=batchtools/templates/cactus/comet.cfg
~~~

If this command is successful you can then compile with

~~~
    $ make whiskythc -j4
~~~

Running
-------

To run a simulation you will need a parfile. Some examples are provided in the
"parfiles" directory. Note that the examples include special strings delimited
by the "@" character that are substituted automatically if you use batchtools.
If you do not want to use batchtools, you will need to replace them manually.

A minimal script to run Cactus using 4 process with 2 threads each would look
like this

~~~
    $ export OMP_NUM_THREADS=2
    $ export CACTUS_NUM_PROCS=4
    $ export CACTUS_NUM_THREADS=2
    $ mpirun -np 4 ./cactus_whiskythc someparfile.par
~~~

I recommend to use [batchtools](http://bitbucket.org/dradice/batchtools) to run
simulations. [simfactory](http://simfactory.org/) can also be used with
WhiskyTHC.

Microphysics Tables
-------------------

The `Data` folder contains tables with several nuclear equations of state
(EOS). If you plan to run with tabulated EOS, you will need to either place the
`Data` folder in your `$HOME` directory, or adapt the parfiles so that the code
can find these tables at runtime.  `THCExtra/tools/eos_tables`, in the
`Cactus/repos` folder, contains scripts that can be used to generate these
tables from those on
[stellarcollapse.org](https://sntheory.org/equationofstate). These can generate
the tables used for the evolution and zero-temperature, beta-equilibrated,
tables to create initial data in different formats
([Lorene](http://www.lorene.obspm.fr/), Pizza,
[RNS](http://www.gravity.phys.uwm.edu/rns/), etc.).

Please note that WhiskyTHC assumes the specific energy density to be always
positive. This is achieved by changing the reference baryon mass in the EOS
(see the `mass_factor` database in the HDF5 files). For consistency, you should
change the `LoreneID::mass_conversion` parameter when reading Lorene initial
data, because Lorene assumes the baryon rest mass to be the atomic mass unit.

Analyze and Visualize Results
-----------------------------

Note that WhiskyTHC uses geometrized units with G = c = Msun = 1.

For basic analysis and visualization of WhiskyTHC results, I developed two python
packages:

* [scidata](https://bitbucket.org/dradice/scidata): utilities to read
Cactus/Carpet data into python for further analysis.
* [scivis](https://bitbucket.org/dradice/scivis): library of 2D visualization
scripts (uses scidata).

Please refer to the `README` files of those packages for instructions on how to
install and use them.

For more advanced visualizations, including 3D volume renderings, you can use
[VisIt](https://wci.llnl.gov/simulation/computer-codes/visit). VisIt already
includes a [plugin to read Cactus/Carpet data](http://cactuscode.org/documentation/visualization//VisIt/).
Note that this plugin can only handle correctly vertex-centered AMR data. It is
still possible to use it to visualize cell-centered AMR data with VisIt and with
this plugin, however this produces small, but visible, artifacts at refinement
level boundaries.

Public Release
--------------

The public release of WhiskyTHC does not include project specific modules and
modules from thirty party that I do not have permission to share. Experimental
modules of WhiskyTHC are also not included.

References
----------

If you find WhiskyTHC to be useful for your research, I ask that you please
cite the following references:

* D. Radice and L. Rezzolla, THC: a New High-Order Finite-Difference
  High-Resolution Shock-Capturing Code for Special-Relativistic Hydrodynamics,
  Astronomy & Astrophysics 547, A26 (2012),
  [arXiv:1206.6502](https://arxiv.org/abs/1206.6502).

* D. Radice, L. Rezzolla, and F. Galeazzi, Beyond Second-Order Convergence in
  Simulations of Binary Neutron Stars in Full General-Relativity,
  Monthly Notices of the Royal Astronomical Society: Letters
  437, L46 (2014), [arXiv:1306.6052](https://arxiv.org/abs/1306.6052)

* D. Radice, L. Rezzolla, and F. Galeazzi, High-Order Fully
  General-Relativistic Hydrodynamics: new Approaches and Tests,
  Classical and Quantum Gravity 31, 075012 (2014),
  [arXiv:1312.5004](https://arxiv.org/abs/1312.5004)
