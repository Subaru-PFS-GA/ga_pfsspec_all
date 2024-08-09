# Development

Starting from source code requires a few steps to set up the environment. First we need a Python environment with all dependencies. Second, we have to clone the main repository and all submodules and make sure they are on the same branch and up to date. Then we need to set a few environment variables and finally, run the init script to initialize the environment for development.

The init script takes of tasks such as creating the symlinks for each submodule, define some important git settings that are not part of the cloned repository and generate the environment file to be picked up by vscode.

## Creating a new Python environment

It is advised to create a new Python environment for development. The environment should be created with the necessary dependencies.

    $ conda env create -n pfsspec -c conda-forge python=3.10

**TODO**: write about the dependencies

## Cloning the repository

The PFSSPEC main repository is ga_pfsspec_all which references several submodules. To clone the repository with all submodules, use the following command:

    $ cd ~/project/Subaru-PFS-GA
    $ git clone --recursive https://github.com/Subaru-PFS-GA/ga_pfsspec_all

Even though this will clone the master branch of the repository at the most recent commit, the submodules might not be at the latest commit. To update the submodules to the latest commit, use the following command:

    $ cd ga_pfsspec_all
    $ ./bin/checkout

which will update all submodules to the latest commit on the branch with the same name as the branch of the main module (master by default).

## Creating a new configuration

Some environmental variables need to be configured. Create a new file `configs/envs/default.sh`. You can look at `configs/envs/example.sh` as an example. Set the following variables according to your systems's configuration.

## Initializing the environment

Once the `default.sh` configuration file is created, you can initialize the environment by running the following command:

    $ source bin/init

It should create a few symlinks and set up the environment for development. The script will also generate a `.env` file that will be picked up by vscode to set up the environment for debugging.

The init scripts also sets up git hooks to strip the output of the notebooks before committing them to the repository. This is to avoid conflicts in the notebooks due to the output cells and to reduce the size of the repository.

## Project structure

The project is divided into several submodules, each of which is a separate Python package. The main repository `ga_pfsspec_all` is a meta-package that contains all the submodules as subpackages.

```
/ 
  - assets      : assets, e.g. build configuration templates
  - bin         : command-line tools, mostly bash scripts
                  some scripts are symlinked from submodules
  - build       : only created when building packages
  - configs     : configuration files
    - envs      : environment configuration files
  - docs        : documentation, subdirectories linked from submodules
  - modules     : submodules
    - core      : core functionality
    - stellar   : stellar spectrum processing
    - survey    : survey data I/O
    - sim       : stellar spectrum simulation
    - learn     : machine learning
  - nb          : notebooks, subdirectories linked from submodules
  - python      : python source code root, symlinked from submodules
    - pfs
      - ga
        - pfsspec
            - core
            - stellar
            - survey
            - sim
            - learn
    - test      : unit tests
  - scripts
```

When the init script is sourced, the symlinks are create such that the submodules are accessible from the main repository by only referencing a single directory in `PYTHONPATH`.

Within each submodule, the structure is similar to the main repository:

/
    - bin         : command-line tools, symlinked to the main repository ./bin
    - nb          : notebooks, symlinked to the main repository ./nb
    - python      : python source code, namespace symlinked under ./python
    - recipes     : conda recipe, auto-generated
    - ups         : EUPS files, auto-generated

## Auto-generated files for packaging

The main package contains the Python script `./bin/build` which implements the tools necessary to build the packages. See [Build](./3_build.md) for more details.

The scipt works by generating all files necessary for packaging. The templates of these files are stored in the `./assets` directory.

When building packages, the version number is generated from the latest git tag of the format `vX.Y.Z` where Z is the number of commits since the last tag. The version number is stored in a `_version.py` file in the package's namespace.

## Development in VSCode

To work on the code in vscode, open the folder of the main repository `ga_pfsspec_all`. From each terminal window, source the init script:

    $ source bin/init

Since it is a large repository, it's advised to exclude certain parts of it. Most auto-generated files are excluded by default from .gitignore. It's important to exclude the `modules` directory from search, otherwise there will be duplicate results since the source directories of the submodules are symlinked under the main repository.

The relevant lines in `settings.json` are

```json
    "search.exclude": {
       "modules": true,
    },
```

## Running Jupyter notebooks

In order to load the modules from source, the PYTHONPATH variable has to be set. VSCode supports it via a `.env` file that's generated by the init script. The relevan lines in `settings.json` are

```json
    "python.envFile": "${workspaceFolder}/.env",
```

Writing unit tests is generally better than debugging from notebooks. Sometimes, especially when the code needs frequent modification to work out an idea or when a large amount of data needs to be kept in memory, notebooks are better. It is advised to only write minimal code in the notebook cells and move the code to the library source files as soon as it works because it makes debugging a lot easier.

## Debugging (from vscode)

The best way to debug Python code is via the `debugpy` package which open a TCP port for the debugger to connect to.

Most command-line scripts are set up for debugging support which is achieved by passing the command to `./bin/run`. The latter script is a wrapper which support starting up the debugger (and many more things, see below). Passing `--debug` will start any command-line script in debug mode waiting for the debugger client (vscode) to connect.

To set up the debugger in vscode, install the python extension and create a `launch.json` file with a "Python Remote Attach" configuration where you specify the same port number as in the environment variable `DEBUGPY_PORT` set up by the `./bin/init` script.

The relevant lines from `launch.json` are

```json
        {
            "name": "Python Debugger: Remote Attach",
            "type": "debugpy",
            "request": "attach",
            "connect": {
                "host": "localhost",
                "port": 5683
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "${workspaceFolder}"
                }
            ]
        }
```

When debugging command-line scripts, simply start the script from the terminal, wail a few seconds until Python is loaded and hit F5. The debugger will connect and you can set breakpoints and inspect variables.

## Unit tests

## Command-line scripts