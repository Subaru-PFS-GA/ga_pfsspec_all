# pfsspec library

Python Library for PFS GA (Stellar) Spectroscopy

The library consits of modules for

* interpolation of grids of synthetic stellar spectra
* fast simulation of observations based on the ETC
* training set generation for machine learning
* training of deep neural networks

## Dependencies

You need [PySynPhot](https://pysynphot.readthedocs.io/en/latest/) for many spectrum operations. [Clone it from github](https://github.com/spacetelescope/pysynphot) or [install as a python package](https://pypi.org/project/pysynphot/). Also download the [PySynPhot CDBS data files](https://www.stsci.edu/hst/instrumentation/reference-data-for-calibration-and-tools/synphot-throughput-tables.html).

To access [SciServer](https://sciserver.org), you need the [SciScript-Python](https://github.com/sciserver/SciScript-Python) client. Clone it from github to your project directory.

An anaconda package list will soon be provided for setting up conda environments.

## Installation (user only)

TBW

## Installation (development)

Clone the main git repository and its submodules under your typical project directory.

    $ git clone --recursive git@github.com:Subaru-PFS-GA/ga_pfsspec_all.git

Check out all submodules to the latest of the `master` branch by running

    $ ./bin/checkout

Create your own environment file under `configs/envs` by copying `example.sh` to `default.sh`. Activate the environment by sourcing the init script from the **root directory** of the `ga_pfsspec_all` project.

    $ cd ga_pfsspec_all
    $ source bin/init

Remember to source this file in all terminals your planning to use for pfsspec development. The script sets important git options that cannot be checked into the repository and commits won't work without them. This includes the script that strips cell outputs from Jupyter notebooks to reduce clutter.