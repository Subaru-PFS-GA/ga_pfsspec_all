# Building the PFSSPEC packages

This section covers generating the necessary files for building the PFSSPEC packages, building the conda packages or EUPS packages and installing them into an environment.

PFSSPEC consits of multiple modules which compile into several conda packages. The build script works by compiling all modules or a single module at a time.

The build process depends on git as it generates the packages version numbers from the lates git tag of the format `vX.Y.Z` where Z is the number of commits since the last tag. The version number is stored in a _version.py file in the package's namespace.

The build subcommands are the following:

* **discover**: find all submodules and print info (dry run)
* **configure**: generate the setup configuration files
* **clean**: remove the files generated during build, except the output files
* **python-build**: build the python packages
* **conda-build**: build the conda packages
* **scons**: run `scons` on each module (under development)
* **eups-create**: create the EUPS packages (under development)
* **python-install**: install the packages using `distutils`
* **pip-install**: install the packages using pip
* **eups-install**: install the packages using EUPS

## Prerequisites

* The main repo `ga_pfsspec_all` must have a valid environment configuration in `configs/envs` and must be initialized with `source bin/init`. See `configure.md` for details. This is necessary to generate all symbolic links from the submodule directories to the main repo and perform other local git configuration steps. Note that sourcing the init script will activate a conda environment that might be different from what you want to use to build the package.
* All folders (except namespace folders) must contain an __init__.py in order to be discovered.
* A _version.py must be present under submodule's namespace. It will
  later be auto-generated from git tags by a build script.
* Building in a fully configured conda environment would take a very long time
  due to the large number of dependencies. It is advised to build in a
  temporary environment.

## Building the python packages

TBW

## Building the conda packages

### Create a temporary environment

The conda-build step requires the package conda-build so it's advised to have a separate environment for building the packages.

This will download the packages to a user directory, so no need for sudo rights.
Do this in a new terminal WITHOUT sourcing bin/init otherwise the wrong python
installation will be picked up by the environment as some CONDA variables are
not reset.

    $ export TMPPKGS=/srv/local/tmp/dobos/conda/pkgs
    $ export TMPENV=/srv/local/tmp/dobos/conda/envs/pfsspec-build

Create the new environment with the minimal necessary dependencies for the build process.

    $ CONDA_PKGS_DIRS=$TMPPKGS conda create --prefix $TMPENV -c conda-forge  python=3.10 conda conda-build mamba git eups

Activate the environment by sourcing the activate script directly from the environment directory. This is necessary to pick up the correct conda executable when running the package build scripts. (Note that the display name of the environment after executing this command will always be `base`.)

    $ source $TMPENV/bin/activate

Verify the environment is activated

    $ which python
    $ echo $CONDA_EXE

... do stuff ...

Install a conda packages requires a temporary directory for the package cache as the system installation is read-only by users.

    $ CONDA_PKGS_DIRS=$TMPPKGS conda install numpy

Remove a user environment

    $ conda deactivate
    $ conda env remove --prefix $TMPENV

## Build the conda packages

To build the conda packages, we use `conda-build`. In addition to packaging libraries, it also manages the json files for the conda channels. Since a conda channel usually contains multiple version of the same package, older versions of the package must not be removed, and the json files must not be edited by hand.

Choose a location for the output packages.

    $ export OUTPUTDIR=/srv/local/tmp/dobos/conda/channels/pfsspec

Configure and build the submodules    

    $ ./bin/build configure
    $ ./bin/build conda-build --package-folder $TMPPKGS --output-folder $OUTPUTDIR

The build script accepts the argument `--module mod1 [ mod2 ... ]` which can be used to limit the submodules being processed.

To test the packages, your can install them into the temporary environment.

    $ CONDA_PKGS_DIRS=$TMPPKGS conda install -c file://$OUTPUTDIR pfsspec-core -c conda-forge


## Build with EUPS

TBW

## Install the package from source with PIP

**TODO**: replace with ./bin/build python-install

The package can be installed with `distutils` (setup.py) or `pip`. Distutils will install it under lib/python3.x/site-packages/pfsspec-*-x.x.x-py3.x.egg which means the egg built will be specific to the python version. Pip will install it directly to the namespace directories, namely lib/python3.x/site-packages/pfs/ga/pfsspec/*

Run setup.py in the root of each submodule

    $ pip install .

Uninstall using pip, from _outside_ the directory of the submodule. Has to be outside of module directory, otherwise pip finds the package under modules/.../python as opposed to ~/envs/lib... and cannot uninstall it (obviously).
