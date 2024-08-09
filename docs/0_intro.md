# Introduction

The PFSSPEC library is a collection of Python modules for the analysis and simulation of stellar spectra. It is designed to be used in the context of the Subaru PFS instrument, but it can be used for any other purpose. The library is divided into several modules, each of which can be used independently. The modules are:

* **core**: Contains the core functionality of the library. This includes very basic spectrum manipulation, data models and I/O.

* **stellar**: Contains functions to model stellar spectra. It implements  synthetic grid interpolation with various interpolation methods, including linear, RBF and spectrum compression with PCA. The module also contains functions to fit stellar spectra with templates with flux correction or continuum estimation.

* **survey**: Contains I/O classes to read data from various surveys in many different file formats.

* **sim**: Contains functions to simulate spectra. It implements a simple model of the PFS instrument but can be extended to other spectrographs as well. Very fast spectrum simulation is based on a tabulated output from the PFS Exposure Time Calculator (ETC).

* **learn**: Machine learning models and training library for stellar spectra. -- library not public yet

