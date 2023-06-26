# Master_thesis
This repository contains the works of Gaetano Granatelli for his Master's thesis at Sapienza University of Rome, titled "Surface composition of Jupiter’s icy moon Ganymede: clues about habitability through spectral unmixing".

## Table of contents
* [Introduction](#Introduction)
* [Spectral Unmixing](#Spectral-Unmixing)

---

## Introduction 
**_Context_**. The study of astrobiologically compelling targets is critical if we are to understand biology as a potentially common phenomenon in our Galaxy and beyond. Liquid water, a keystone of habitable environments for life as we know it, was discovered to be hosted as a global ocean beneath Ganymede’s icy crust, providing conceptual basis within which new theories on habitability can be constructed.

_**Aims**_. Investigation of Ganymede’s surface composition, that is presented in this work, could provide a window into the habitability of its oceans. Following a computational approach well-established within the remote-sensing scientific community, we derive our study from a _linear mixing_ modeling of a hyperspectral mosaic acquired by the Near-Infrared Mapping Spectrometer (NIMS), onboard the NASA Galileo spacecraft (1995-2003). We aim at deriving abundance maps of constituent spectral endmembers, related to pure species measured in the laboratory, under the assumption that they combine linearly to produce the surface’s measured spectral profiles.

_**Methods**_. The Galileo/NIMS mosaic, in units of calibrated radiance factor (I/F), is imported, processed and photometrically corrected. By exploiting a library of reference spectral endmembers measured in the laboratory, an unmixing model is implemented through a fully constrained least squares minimization (FCLS) algorithm, applied to a subset of spectra representative of selected regions of interest on the surface. The spectra are iteratively modeled with different combinations of endmembers, the best of which provides abundance values and related maps describing the chemical composition of the uppermost millimeter-thick surface layer.

---
	
## Spectral Unmixing
Section ["Observations and Dataset"](#Observations-and-Dataset) introduces the hyperspectral dataset from the Galileo/NIMS instrument. Data are read, displayed and processed in Python. Section ["Description of Spectral Features"](#Description-of-Spectral-Features) and Section ["Regions Of Interest"](#Regions-Of-Interest) provide descriptions of selected spectra from specific regions of interest, well-representative of the spectral diversity of the surface. This set of spectra is the one given to the linear spectral mixing model described in Section ["Linear Mixture Spectral Modeling"](#Linear-Mixture-Spectral-Modeling) for abundance retrieval. We will rely on an existing database of laboratory reference spectra, acquired at a temperature representative of the studied surface, to decompose the measured spectral profiles into a limited number of components and their relative abundances. Finally, Section ["Compositional Maps"](#Compositional-Maps) presents best-estimate abundance values of the dif- ferent endmembers identified by the model, providing maps describing the chemical composition of the uppermost millimeter-thick surface layer. 

### Pipeline
* [Useful functions](#Useful-functions)
* [Observations and Dataset](#Observations-and-Dataset)
* [Description of Spectral Features](#Description-of-Spectral-Features)
* [Regions Of Interest](#Regions-Of-Interest)
* [Linear Mixture Spectral Modeling](#Linear-Mixture-Spectral-Modeling)
* [Compositional Maps](#Compositional-Maps)

The Python packages required for running all functions are:

```python
### Visualization tools (es. plots)
import matplotlib
import matplotlib.pyplot as plt  
from matplotlib.ticker import FormatStrFormatter, MultipleLocator
from matplotlib import pyplot as plt, cm
from mpl_toolkits.axes_grid1.inset_locator import inset_axes
import matplotlib.patches as patches

### Data analysis and manipulation tools 
import pandas as pd          # Creation of Data Frames 
import numpy as np           # Fundamental package for scientific computing
import numpy.ma as ma        # Support for data arrays with missing or invalid entries
import scipy as sp           # Fundamental algorithms for scientific computing
import scipy.stats as stats  # Chi2 test
from scipy import signal     # Savitzky-Golay filter (smoothing data)
import sys, os               # Tools to deal with filenames, paths, directories
from PIL import Image        # Python Imaging Library
from array import array
import math

### Specific useful packages
import cartopy.crs as ccrs                 # Produce maps and other geospatial data analyses
import pysptools                           # Find abundance values with FCLS method
from spectral import *                     # Module for processing hyperspectral image data
from spectral import imshow, view_cube     # Specific packages
import spectral.io.envi as envi

### Collection of Python tools for astronomy (credits: Oliver King)
import astro_tools_master   
sys.path.append('/Users/gransss/Downloads/astro-tools-master/tools')
import tools
```

Many of these are automatically included in Anaconda distributions. Additional modules, like the `astro_tools_master` (credits to [Oliver King](https://github.com/ortk95/astro-tools)) can be installed using the usual `conda install ...` or pip `install ...`. 

---

### Useful functions

Some useful, recurring functions which are used in the code. They are found in the file [Useful functions](Useful_functions/Useful_functions.md)

---

### Instrument, Observations and Dataset

> After the Jupiter orbit insertion (JOI) maneuver, on December 7, 1995 Galileo began its primary mission, a two-year study of the Jovian system. Galileo traveled around Jupiter in elongated ovals—each orbit lasted about two months. The orbits were designed for close-up flybys of Jupiter’s largest moons: the orbiter tour included four close encounters with Ganymede, three with Europa, and three with Callisto.

The NIMS observation named **G1GNGLOBAL01A1** is used in this study, as summarized in the following Table. It refers to the first Galileo close encounter with Ganymede, with observation covering Ganymede’s anti-Jovian hemisphere centered near (10°S, 180°W), at 228 wavelengths and an approximate spatial resolution of 100 km per pixel. 

<img width="714" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/3e518267-ed3c-4dfb-a589-7b6b3d1f7b9a">

---

**Dataset G1GNGLOBAL01A1**

For handling the dataset we use [Spectral Python](http://www.spectralpython.net/), an open source Python module distributed under the MIT License for processing hyperspectral image data. It has functions for reading, displaying, manipulating, and classifying hyperspectral imagery. 

The natural output of an imaging spectrometer like NIMS is a **hyperspectral data cube**, enabling the data to be analyzed spatially (an image at a time) or spectrally (a spectrum at a time). The structure of NIMS spectral image cubes follows Planetary Data System _qube_-object standards. The NIMS data cube for the dataset **G1GNGLOBAL01A1** was available in its reduced form[^note2], along with all the observation geometry parameters needed for our analysis. The cube includes a total of 228 × 120 × 100 pixels calibrated in **radiance factor** r_F = \pi*r (where the _r_ is the bidirectional reflectance of the surface). 

| Name        | Description      |
| :------------- |:------------- | 
| g1g002ci.LBL      | Text file containing a description of the data and the associated labels, following the standards of the Planetary Data System |
| g1g002ci.qub.dat      | Data cube calibrated in radiance factor also known as **I/F**, which is the ratio of the measured radiance to that of a Lambertian surface illuminated and viewed from the normal direction, at the same distance from the Sun.  |  
| g1g002ci.qub.dat.hdr |   Header information relative to the file _g1g002ci.qub.dat_ |   
| g1g002ci.qub.geo | File containing the so called 'backplanes', concerning geometrical parameters of the planetary observation. |
| g1g002ci.qub.geo.hdr | Header information relative to the file _g1g002ci.qub.geo_|

The code processing the hyperspectral data cube G1GNGLOBAL01A1 is found in the directory [Dataset](#Instrument_Observations_and_Dataset/01_Dataset.md).

---

Spatially, the cube is composed of 228 images, each one composed of 120 × 100 pixels; spectrally, the cube hosts 12000 spectra, one for each pixel. The cube and the spectra are shown in the following Figure, together with some geometric backplanes. The mean and median spectra are also shown, where they are computed over the pixels belonging to Ganymede’s dayside, i.e., all the pixels with angle of incidence i < 90°.

<img width="487" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/92fee51e-89ed-4ab6-8cd0-16fadb2b2b39">

> The _calibrated_ radiance factor, indicated with **I/F**, is the ratio between the radiance measured by the instrument on the target and the solar radiance scaled for the heliocentric distance of the target expressed in AU. In this way, I/F is a dimensionless quantity describing the reflectivity of a target as a function of wavelength, having automatically excluded the spectral signatures of solar nature. 
> **I/F is _not_ a reflectance**, since it does not account for **_photometric effects_** due to the peculiar illumination/observation geometry, and moreover it can include the target’s thermal emission (which however is negligible for Ganymede up to 5 micrometers). In the Section [Photometric Correction](##Instrument_Observations_and_Dataset/03_Photometric_correction.md) it will be explained how this correction was applied in the data. 


[^note2]: The data reduction routine transforms the raw observations into cleaned and calibrated data sets that are ready for scientific use. The reduction accounts for detector properties and observation conditions to ensure that observations are calibrated and compatible.

---









**NIMS instrumental errors**

To estimate the NIMS instrument error of the mean spectrum, a coarse-grained procedure is used in order to obtain a spectrum of instrument errors associated with the radiance factor I/F, i.e., the error bars to be associated with the mean spectral profile. 

Because the variations in target spectral radiance of interest are often small in magnitude and are difficult to measure precisely, considerable interest is created in the definition and measurement of the capability of a sensor to respond to small spectral radiance changes. This capability, related to sensitivity, is commonly represented in the noise metric of the signal-to-noise ratio (SNR), but it is often described also in terms of **Noise Equivalent Spectral Radiance (NESR)**, which is the minimum detectable variation in spectral radiance. 
> The NESR of a scanner system is defined as _**the amount of change in spectral radiance required to produce a signal equivalent to the system’s noise**_. Stated another way, this is the quantity of spectral radiance that produces a signal-to-noise ratio of unity. An input radiance change that will produce an output signal equal to the noise (SNR = 1) is the minimum detectable change in radiance level. Thus, NESR is the minimum detectable radiance and is defined as the resolution of the system.


The code showing the procedure is shown in the directory [NIMS instrumental errors](#Instrument_Observations_and_Dataset/02_NIMS_errors.md) it will be explained how this correction was applied in the data. 

---

#### Photometric correction for i<90° data





---

### Description of Spectral Features


---

### Regions Of Interest

---

### Linear Mixture Spectral Modeling

---

### Compositional Maps

