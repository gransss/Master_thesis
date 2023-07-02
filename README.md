# Master_thesis
This repository contains the works of Gaetano Granatelli for his Master's thesis at Sapienza University of Rome, titled "Surface composition of Jupiter’s icy moon Ganymede: clues about habitability through spectral unmixing".

## Table of contents
* [Introduction](#introduction)
* [Spectral Unmixing](#spectral-unmixing)

---

## Introduction 
**_Context_**. The study of astrobiologically compelling targets is critical if we are to understand biology as a potentially common phenomenon in our Galaxy and beyond. Liquid water, a keystone of habitable environments for life as we know it, was discovered to be hosted as a global ocean beneath Ganymede’s icy crust, providing conceptual basis within which new theories on habitability can be constructed.

_**Aims**_. Investigation of Ganymede’s surface composition, that is presented in this work, could provide a window into the habitability of its oceans. Following a computational approach well-established within the remote-sensing scientific community, we derive our study from a _linear mixing_ modeling of a hyperspectral mosaic acquired by the Near-Infrared Mapping Spectrometer (NIMS), onboard the NASA Galileo spacecraft (1995-2003). We aim at deriving abundance maps of constituent spectral endmembers, related to pure species measured in the laboratory, under the assumption that they combine linearly to produce the surface’s measured spectral profiles.

_**Methods**_. The Galileo/NIMS mosaic, in units of calibrated radiance factor (I/F), is imported, processed and photometrically corrected. By exploiting a library of reference spectral endmembers measured in the laboratory, an unmixing model is implemented through a fully constrained least squares minimization (FCLS) algorithm, applied to a subset of spectra representative of selected regions of interest on the surface. The spectra are iteratively modeled with different combinations of endmembers, the best of which provides abundance values and related maps describing the chemical composition of the uppermost millimeter-thick surface layer.

---
	
## Spectral Unmixing
Section [Observations and Dataset](#observations-and-dataset) introduces the hyperspectral dataset from the Galileo/NIMS instrument. Data are read, displayed and processed in Python. Section [Regions Of Interest](#regions-of-interest) provides descriptions of selected spectra from specific regions of interest, well-representative of the spectral diversity of the surface. This set of spectra is the one given to the linear spectral mixing model described in Section [Linear Mixture Spectral Modeling](#linear-mixture-spectral-modeling) for abundance retrieval. We will rely on an existing database of laboratory reference spectra, acquired at a temperature representative of the studied surface, to decompose the measured spectral profiles into a limited number of components and their relative abundances. Finally, Section [Compositional Maps](#compositional-maps) presents best-estimate abundance values of the dif- ferent endmembers identified by the model, providing maps describing the chemical composition of the uppermost millimeter-thick surface layer. 

### Pipeline
* [Useful functions](#useful-functions)
* [Observations and Dataset](#observations-and-dataset)
* [Regions Of Interest](#regions-of-interest)
* [Linear Mixture Spectral Modeling](#linear-mixture-spectral-modeling)
* [Compositional Maps](#compositional-maps)

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

### Observations and Dataset

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

The code processing the hyperspectral data cube G1GNGLOBAL01A1 is found in the directory [Dataset](Instrument_Observations_and_Dataset/01_Dataset.md).

---

Spatially, the cube is composed of 228 images, each one composed of 120 × 100 pixels; spectrally, the cube hosts 12000 spectra, one for each pixel. The cube and the spectra are shown in the following Figure, together with some geometric backplanes. The mean and median spectra are also shown, where they are computed over the pixels belonging to Ganymede’s dayside, i.e., all the pixels with angle of incidence i < 90°.

<img width="487" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/92fee51e-89ed-4ab6-8cd0-16fadb2b2b39">

> The _calibrated_ radiance factor, indicated with **I/F**, is the ratio between the radiance measured by the instrument on the target and the solar radiance scaled for the heliocentric distance of the target expressed in AU. In this way, I/F is a dimensionless quantity describing the reflectivity of a target as a function of wavelength, having automatically excluded the spectral signatures of solar nature. 
> **I/F is _not_ a reflectance**, since it does not account for **_photometric effects_** due to the peculiar illumination/observation geometry, and moreover it can include the target’s thermal emission (which however is negligible for Ganymede up to 5 μm). In the Section [Photometric Correction](Instrument_Observations_and_Dataset/03_Photometric_correction.md) it will be explained how this correction was applied in the data. 


[^note2]: The data reduction routine transforms the raw observations into cleaned and calibrated data sets that are ready for scientific use. The reduction accounts for detector properties and observation conditions to ensure that observations are calibrated and compatible.

---


**NIMS instrumental errors**

To estimate the NIMS instrument error of the mean spectrum, a coarse-grained procedure is used in order to obtain a spectrum of instrument errors associated with the radiance factor I/F, i.e., the error bars to be associated with the mean spectral profile. 

Because the variations in target spectral radiance of interest are often small in magnitude and are difficult to measure precisely, considerable interest is created in the definition and measurement of the capability of a sensor to respond to small spectral radiance changes. This capability, related to sensitivity, is commonly represented in the noise metric of the signal-to-noise ratio (SNR), but it is often described also in terms of **Noise Equivalent Spectral Radiance (NESR)**, which is the minimum detectable variation in spectral radiance. 
> The NESR of a scanner system is defined as _**the amount of change in spectral radiance required to produce a signal equivalent to the system’s noise**_. Stated another way, this is the quantity of spectral radiance that produces a signal-to-noise ratio of unity. An input radiance change that will produce an output signal equal to the noise (SNR = 1) is the minimum detectable change in radiance level. Thus, NESR is the minimum detectable radiance and is defined as the resolution of the system.


The code showing the procedure is shown in the directory [NIMS instrumental errors](Instrument_Observations_and_Dataset/02_NIMS_errors.md). The steps of the procedure are shown in the Figure: 

<img width="502" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/fecc5e24-9237-4e5a-99af-67fbf7065d2a">


---

#### Photometric correction for i<90° data

The application of a photometric correction is an essential step in obtaining a map expressing the surface variability in terms of composition and physical state. A sphere of uniform albedo illuminated by a point-like source (i.e., the Sun) appears to have varying brightness, with brightness dropping off toward the edge of the disk. In fact, the brightness of an object depends on the intrinsic properties of the surface materials (composition, grain size, roughness, porosity etc.) and the surface shape, but _also_ on the geometry of illumination and observation. To compare surface spectral reflectance from one region to another observed under different geometries, and interpreting the composition based on laboratory measurements (taken at geometries different from the planetary observations), a pixel-by-pixel photometric correction is required to convert the calibrated radiance factor I/F into **spectral reflectance**. The correction takes the form:

`Corrected image = Image in units of I/F × Photometric model`

Several models have been developed: in this study we adapt the approach of Shkuratov et al., which uses the Akimov model to correct the radiance factor I/F in NIMS observations for photometric effects. The steps of the procedure are shown in the Figure, and they can be found in the directory [Photometric Correction](Instrument_Observations_and_Dataset/03_Photometric_correction.md).

<img width="539" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/0767543d-0f8b-486f-b136-a18c2293d4f9">


---

#### Filtering the (photometrically corrected) data

Finally, a **filtering** on the data cube is applied on the incidence and emission angles. Data with extreme emission and incidence angle (> 60°) have large residual photometric errors, and above these angles the spatial resolution of the mapped data degrades significantly. For this reason, they are not used in this study. 

> Incidence angle in [0,60°], emission angle in [0,60°]
```Python
# 2D MASK (for both the requests)
mask_ie = (INCIDENCE_ANGLES>0)&(INCIDENCE_ANGLES<60)&(EMISSION_ANGLES>0)&(EMISSION_ANGLES<60)
FILTER_rows, FILTER_cols = np.where(mask_ie)

# 3D MASK
mask_ie_3D = np.zeros(g1g002_raw.shape, dtype=bool)
for t in range(len(wavelength)):
    mask_ie_3D[t,:,:] = mask_ie
print("The mask called 'mask_ie_3D' has a shape of: ", mask_ie_3D.shape)   # Output: (228, 120, 100)
print('The element (0,0) is:    {}\n   (the corresponding element'
      ' of the associated array is valid and is said to be unmasked)'.format(mask_ie[0,0]))   # Output: False

# Plot the mask
fig, ax = plt.subplots(figsize=(5,5))
line_sample(ax, 'Mask (0°<i<60°, 0°<e<60°)\nWHITE: masked\nBLACK: unmasked')
ax.imshow(mask_ie_3D[0], cmap=cm.gray)
circle = plt.Circle((50,60), 46.5, alpha=0.5, color='white', ls='--', linewidth=0.5, fill=False)
ax.add_patch(circle)
plt.show()
#fig.savefig('finalMASK.svg', transparent=True, format='svg', dpi=600)

# USE THE MASK ON THE DATA
g1g002_MASK_ie = np.ma.masked_array(g1g002_akimov_adj, ~mask_ie_3D)
max_refl = np.amax(g1g002_akimov)
```

<img width="274" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/75296447-df1c-4498-bc3e-ca7cfd13cdfa">

> Mask the photometrically corrected, adjusted and smoothed data
```Python
fig, (ax1, ax2) = plt.subplots(1,2, figsize=(14,7))
fig.subplots_adjust(wspace = 0.15)
hsi_image(ax1, 'G1GNGLOBAL01A (0.7-$\mu m$ image)\nReflectance (dayside)', np.ma.masked_array(g1g002_akimov, ~mask_i_3D), 0, 'Reflectance')
ax2.set_xlim(0, 99)
ax2.set_ylim(119, 0)
ax2.set_xlabel('SAMPLE', fontsize=8, loc='left')
ax2.set_ylabel('LINE', fontsize=8, loc='top')
ax2.yaxis.set_label_coords(-0.02,0.07)
ax2.xaxis.set_label_coords(0.05,-0.03)
major_ticksx = (0,25,50,75,100)
minor_ticksx = np.arange(0, 100, 1)
major_ticksy = (0,25,50,75,100)
minor_ticksy = np.arange(0, 120, 1)
ax2.set_xticks(major_ticksx)
ax2.set_xticks(minor_ticksx, minor=True)
ax2.set_yticks(major_ticksy)
ax2.set_yticks(minor_ticksy, minor=True)
ax2.set_title('G1GNGLOBAL01A (0.7-$\mu m$ image)\nReflectance (0°<i<60°, 0°<e<60°)')
ax2.imshow(g1g002_akimov[0,:,:], cmap=cm.turbo, alpha=0.4) 
plot = ax2.imshow(g1g002_MASK_ie[0,:,:], cmap=cm.turbo)
cbar = plt.colorbar(plot, ax=ax2, location='bottom',fraction=0.036, pad=0.1)
cbar.ax.set_title('Reflectance', fontsize=10)
circle2 = plt.Circle((50,60), 46.5, alpha=0.8, color='white', linewidth=0.5, ls='--', fill=False)
ax2.add_patch(circle2)
max_refl = np.amax(g1g002_akimov[0])
plot.set_clim(0, max_refl)
plt.show()
```

<img width="741" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/b070fbfe-d1ad-497e-9e4f-4acee7d150de">


The filtered data cube is then projected onto a 360 × 180 pixel array using an equirectangular projection (useful functions can be found in [Useful functions](Useful_functions/Useful_functions.md). Some examples of projections are shown [here](Useful_functions/Projections_on_equirect_map.md)). The Figure shows the dataset ready for the subsequent analysis:


<img width="505" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/c3e79383-6a6e-47d8-b77d-ba18167801fe">

---


### Regions Of Interest
> Ganymede’s surface has long been known to be dominated by H2O-ice mixed with varying amounts of visually darker non-ice compounds. The spectral properties of icy surfaces depend on the refractive indices of H2O-ice, its particle size distribution, the depth and the density of the ice layer and the amount of non-ice impurities present.

To determine the most suitable laboratory reference spectra to be used as inputs for the spectral modeling, we first extract a set of spectra from selected regions of interest on Ganymede (see the Figures), well-representative of the spectral diversity of the surface in the near-infrared wavelength range. 

<img width="498" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/2ada8d1d-b75c-4e4f-aabb-c85e4a0f8557">

<img width="499" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/5f06e4f2-bd15-4761-82cc-d43103b1bc9f">

> As expected, most areas are largely dominated by H2O-ice diagnostic absorptions at 1.04, 1.25, 1.5 and 2.02 μm, which can be used to provide a simple qualitative indication of the water-ice distribution on the surface, with stronger water-ice absorption corresponding to higher water-ice abundance. Depending on crystallinity and temperature additional absorptions at 1.57 and 1.65 μm can occur. Although water-related absorption features dominate the spectra in the 1- to 2.5-μm region, the OH-stretch vibration absorption due both to water or OH-bearing minerals produces a strong absorption near 3 μm in all spectra. Absorptions beyond 3 μm are generally strongly saturated. Nevertheless, a 3.1-μm reflection peak due to water ice is noticeable in spectra of icier terrain and less so in less icy terrain. Furthermore, a reflectance peak at 3.6 μm can often be seen in the spectra of fine-grained H2O ice. Beyond these wavelengths, only an absorption feature at 4.25 μm is present, likely due to CO2.

> There are spectral indicators for the physical state of H2O ice. On Ganymede’s surface H2O can exists in three distinct solid configurations (amorphous, cubic, hexagonal) depending on the condensation temperature, condensation rate, and temperature history. In near-infrared spectra, crystalline and amorphous H2O ice can be distinguished from each other by the combined analysis of the depth of the absorption at 1.65-μm, the shape and band position of the absorption at 2 μm, and the strength and shape of the 3.1-μm reflectance peak. In case of both, amorphous and crystalline H2O ice, present on an icy surface a shift in the wavelength positions of the H2O ice absorptions should be apparent. In addition, amorphous H2O ice is often indicated by a weak or absent absorption at 1.65 μm and a suppressed Fresnel reflection peak at 3.1 μm. However, increasing temperatures and decreasing grains sizes have similar effects on spectra of crystalline H2O ice; and differences in the band position of the strong absorption at 1.5 and 2 μm are only visible, when these absoprtions are not affected by saturation as observed for large particle sizes (> 500 μm) or by unsuspected presence of a component of non-ice but hydrated material.



---

### Linear Mixture Spectral Modeling

---

### Compositional Maps

