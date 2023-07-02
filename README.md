# Master_thesis
This repository contains the work of Gaetano Granatelli for his Master's thesis at Sapienza University of Rome, titled "_Surface composition of Jupiter’s icy moon Ganymede: clues about habitability through spectral unmixing_".

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


The code showing the extraction of the set of spectra from selected regions of interest on Ganymede is shown in the directory [Regions Of Interest](Regions_of_interest/ROI.md).


---

### Linear Mixture Spectral Modeling

After studying the spectral diversity of Ganymede’s surface qualitatively, the next step consists in getting quantitative estimates of the surface composition through spectral modeling. **Linear spectral unmixing** is a deconvolution technique based on the principle that each measured spectrum is a linear combination of spectra of different species, each species assumed to be present in a specific mixture with a relative concentration. The technique uses a library of laboratory reference spectra (acquired at a temperature representative of the studied surface) to perform a best fit of the spectra, and it eventually reduces to an optimisation problem where the spectral library `E` is used to decompose a target spectrum `Y` into `q` components and their relative abundance `a`.

We use such a linear spectral modeling to fit the observed spectra of the selected regions of interest, with the aim to produce compositional maps of Ganymede’s observed region. We treat each pixel’s observed spectrum as a linear combination of discrete endmembers `E_j(λ)` with respective abundances `a_j`, where the modeled spectrum `Y_i` for the _i_-th pixel in the image is calculated as:

<img width="253" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/e1291b69-91b9-4f6e-add2-8487a79c2400">

where:
* `N` is the number of pixels in the image
* `q` is the number of endmembers
* `a_ij` is the abundance of the j-th endmember in the i-th pixel
* `E_j` is the spectral signature of the j-th endmember
* `Y_i` is the spectral signature of the i-th pixel

The abundances `a_j` are subject to two physical constraints: the non-negativity constraint (**NC**) which ensures that the individual abundances are all physically realistic values (0% to 100%), and the sum-to-one constraint (**SC**) which ensures that the fractional abundance of the different endmembers sums to 100%:

<img width="328" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/443c3bb5-9cbb-4115-a914-d1536e93bd13">

**Note**
> Linear unmixing is a **first-order approach** which is valid as long as the observed spectrum for each pixel results in the spatial (hence linear) mixing of several spectral endmembers. This is the case for the observations of Europa and Ganymede, yielding relatively coarse spatial resolution (∼ 100 km). By definition, linear unmixing does not account for any nonlinear scattering that occurs in the uppermost mm-thick surface layer, which may therefore be responsible for some post-fitting residuals. Because H2O-ice and the non-ice materials can be intimately mixed at the grain scale, the most quantitative analysis of their spectral properties would require a comprehensive _radiative transfer modeling_ (Hapke, 2002). Fortunately, previous studies demonstrated that H2O-ice and non-ice surface materials on Ganymede occur in discrete patches, likely the result of cold-trapping of H2O-ice in patches and lag deposits of dark non-ice material through downslope mass-movement. Consequently, linear mixture modeling of these satellites’ surfaces is a useful approximation to investigate the quantitative estimates of the H2O-ice and non-ice abundance, and of H2O-ice particle size.


The schematic diagram of the hyperspectral unmixing process is shown in the Figure: 

<img width="490" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/6ddb34f9-b034-41ed-8910-827512344d00">


---

#### Spectral library

The full list of endmembers in our spectral library is given in the following Table, and selected spectra are shown in the Figure. 

<img width="576" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/8cc56431-c062-4a2c-bf7c-3f3af6a24337">

<img width="490" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/ea5270bd-a976-49df-891d-ef94de252ba7">

Most published laboratory spectra are acquired at room or Martian temperature, so cryogenic laboratory spectra acquired at temperatures relevant for the icy satellites of the outer Solar System are relatively uncommon. To be representative of Ganymede’s surface conditions (∼ 125 ± 25 K), for our library we considered only those cryogenic spectra acquired in a temperature range from 80 K to 160 K. 

These laboratory spectra can be separated in three types: **(i)** H2O-ice, **(ii)** hydrated sulfuric acid [Carlson et al., 1999], and **(iii)** a variety of mineral salts such as sulfates, carbonates and chlorides salts [Dalton, 2007]. These compounds are known or expected to be present on the surface of Ganymede based on previous literature and models. Finally, another component is required for modeling: a **darkening agent**, spectrally flat over the entire wavelength range. This artificial endmember actually plays the role of the darkening, non-ice material in order to bring the modeled albedo levels to match those that are observed. 
> Note that the sulfuric acid spectra do not cover wavelengths below 1 μm, so this limits our modeling to cover the 1–2.5 μm spectral range. Our spectral library includes all available laboratory spectra covering the Galileo/NIMS wavelength range measured in Ganymede-like conditions, and particular care was taken to ensure the inclusion of the various non-ice species detected in previous works. We also include ‘synthetic’ black and white spectra which are used to model spectrally-flat (e.g., silicate) material.

As the main objective of this study is to get an overview of the chemistry across Ganymede’s surface, the spectral modeling has to be performed on every pixel to obtain abundance maps. These maps will provide information of the uppermost millimeters-thick surface layer about the distribution on the different endmembers used in the model.


The code showing the spectral unmixing procedure is shown in the directory [Linear Mixture Spectral Modeling](Linear_Mixture_Spectral_Modeling/Linear_unmixing.md).


---

### Compositional Maps

For implementing the [spectral algorithm](#linear-mixture-spectral-modeling) described before, we use the Python module [PySptools](https://pysptools.sourceforge.io/index.html). Specifically, we use the **Fully Constrained Least Squared algorithm** to generate and plot abundance maps. This algorithm performs fully constrained (NC and SC) least squares of each pixel in the data using the endmember signatures of the spectral library. This produces abundance distributions for each sampled location to create maps of best-estimate abundances for different species, delivering compositional information of the uppermost mm-thick surface layer.

We applied the linear mixture model to the spectra of the [selected regions of interest](#regions-of-interest). We adopt an iterative approach to unmixing, starting with a small set of endmembers and then increasing to a larger set to improve the fit, modeling the spectra with different combinations of the endmembers. The quality of the fit is evaluated by a visual, qualitative comparison of the model and measured spectra, and is quantified by the chi-squared value. 

---
#### Best-fit unmixing: thirteen end-members
The model that results in the best match to the Ganymede spectrum is composed of thirteen endmembers belonging to four different mineralogical families: (i) H2O-ice; (ii) a darkening agent; (iii) sulfuric acid hydrate; and (iv) salts, one Mg-sulfate (hexahydrite), one Na/Mg-sulfate (bloedite), one Na-sulfate (mirabilite), one Mg-chloride (bischofite), two halides (hydrohalite and ammonium chloride) and two carbonates (natrite and thermonatrite). Mixtures of these thirteen endmembers provide satisfactory fits of most of the regions of interest considered in our study, with the greatest discrepancies visible for Osiris crater. Best fits from four representative regions of interest are shown in the Figure, together with the predicted abundance distributions. 

<img width="491" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/f608f5e5-9106-4ae6-8934-0189013f4f39">

Numerical values for all the seven regions of interest are provided in the following Table:

<img width="503" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/83f7e639-a310-46e7-99d1-93f89272de9f">

> **Spectrally flat material**. In terms of abundances, the spectral modeling of NIMS data showcases the dominance of dark non-ice material over all the other species on Ganymede’s surface, ranging from 5 − 10% to almost 90%, with a mean value of 61.2%. On a regional scale, this darkening agent shows a considerable concentration in the geologically older dark terrains, with abundances reaching over 80% in some zones of the Galileo and Marius Regiones.

> **Water ice**. The way H2O-ice is distributed across Ganymede’s surface largely corresponds to variations in the albedo as seen in the Galileo/SSI optical images. As already found by [Ligier et al., 2019] and [King et al., 2022], the total H2O-ice abundance map (composed of the sum of the different grain sizes we considered) is highly anti-correlated to the dark component’s abundance map, with highest abundance in geologically young bright terrain and visually bright craters where impact events have excavated and exposed relatively clean and contaminated ice form Ganymede’s crust. The highest abundance almost reaches 45% at Osiris crater and Babylon Sulci, and thus dominates their spectral signature. In contrast, the regiones of the dark ancient terrain show the lowest amount of H2O-ice, expressed by a low visible albedo and strongly reduced H2O-ice signature because of the presence of visually darker non-ice material. There are differences in terms of spatial distribution between the different grain sizes of the H2O-ice. The known trend of increasing ice abundance with increasing latitude (with an enrichment at Ganymede’s higher latitudes, often referred to as polar caps) has been found to be accompanied by a decreasing in H2O-ice grain size, with grains smaller than 10 μm being prevalent in the polar regions, whereas large grain sizes (500 μm - 1 mm) are found in the equatorial regions [Stephan et al., 2020], [Ligier et al., 2019], [King et al., 2022]. This can be only partially found in the abundance maps shown in Figure 3.16. Greatest discrepancies are found for the large grain size map (200 μm in our study), which shows an unexpected distribution differing from the results found by Ligier and King.

> **Sulfuric acid**. In contrast to Europa, Ganymede’s sulfuric acid hydrate abundance is quite low, just below 2% overall, and its inhomogeneous distribution is globally known to be characterized by a latitudinal gradient, with abundance increasing with increasing latitude suggesting an exogenic origin [Ligier et al., 2019]. Overall, the spatial distribution is uneven and not correlated with geomorphology.

> **Salts**. The suggested species have a low mean total abundance about 20%. Qualitatively, the total salt abundance map does not show clear correlation with any other maps previously presented. As with previous analyses of NIMS observations [McCord et al., 2002], [Dalton, 2007], [Shirley et al., 2010], Mg- and Na-bearing sulfates and chlorides salts appear to be the main endogenous species on Ganymede’s surface. Abundance maps of the studied surface seem to imply that the highest abundances in Mg-sulfate MgSO4 · 6 H2O (hexahydrite) largely coincide with extensively tectonically resurfaced portions of Ganymede’s bright terrain, together with the bright Osiris crater. Some areas of Uruk and Sippar Sulci, together with the southern portion of Xibalba Sulcus and Babylon Sulci in Ganymede’s leading hemisphere, appear to be the richest areas for hexahydrite. Concerning the distributions of the other three most-abundant salt species: Na2Mg(SO4)2·4H2O (bloedite), NaSO4 · 10 H2O (mirabilite), and NaCl · 2 H2O (hydrohalite), they also seem to be associated with younger bright terrain (except for mirabilite), but they also appear slightly correlated with high-latitude regions, potentially associated with energetic exogenic processes. The abundance maps of the other salts considered in the modeling (bischofite, ammonium chloride and two carbonates) show that their distribution correlates just locally with specific spots distributed all over the studied area. No correlation with the geomorphology (regional scale) is evident.


The best-estimate abundance maps are shown in the following Figure:

<img width="490" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/3591221c-41cb-41c0-8fcd-e99ede9a5cec">

The code for the best-fit specetral unmixing and the compositional maps is shown in the directory [Compositional Maps](Compositional_Maps/Compositional_maps.md).

