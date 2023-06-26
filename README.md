# Master_thesis
_Title_ Surface composition of Jupiter’s icy moon Ganymede: clues about habitability through **spectral unmixing** technique.

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
Section ["Instrument, Observations and Dataset"](#Instrument,-Observations-and-Dataset) introduces the hyperspectral dataset from the Galileo/NIMS instrument. Data are read, displayed and processed in Python. Section ["Description of Spectral Features"](#Description-of-Spectral-Features) and Section ["Regions Of Interest"](#Regions-Of-Interest) provide descriptions of selected spectra from specific regions of interest, well-representative of the spectral diversity of the surface. This set of spectra is the one given to the linear spectral mixing model described in Section ["Linear Mixture Spectral Modeling"](#Linear-Mixture-Spectral-Modeling) for abundance retrieval. We will rely on an existing database of laboratory reference spectra, acquired at a temperature representative of the studied surface, to decompose the measured spectral profiles into a limited number of components and their relative abundances. Finally, Section ["Compositional Maps"](#Compositional-Maps) presents best-estimate abundance values of the dif- ferent endmembers identified by the model, providing maps describing the chemical composition of the uppermost millimeter-thick surface layer. 

### Pipeline
* [Useful functions](#Useful-functions)
* [Instrument, Observations and Dataset](#Instrument,-Observations-and-Dataset)
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

The code processing the hyperspectral data cube G1GNGLOBAL01A1 is found in the directory [Dataset](#Instrument,-Observations-and-Dataset/01_Dataset.md).

---

Spatially, the cube is composed of 228 images, each one composed of 120 × 100 pixels; spectrally, the cube hosts 12000 spectra, one for each pixel. The cube and the spectra are shown in the following Figure, together with some geometric backplanes. The mean and median spectra are also shown, where they are computed over the pixels belonging to Ganymede’s dayside, i.e., all the pixels with angle of incidence i < 90°.

<img width="428" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/34607a26-631f-4ae9-95a6-f4f11a2fe534">

> The _calibrated_ radiance factor, indicated with **I/F**, is the ratio between the radiance measured by the instrument on the target and the solar radiance scaled for the heliocentric distance of the target expressed in AU. In this way, I/F is a dimensionless quantity describing the reflectivity of a target as a function of wavelength, having automatically excluded the spectral signatures of solar nature. 
> **I/F is _not_ a reflectance**, since it does not account for **_photometric effects_** due to the peculiar illumination/observation geometry, and moreover it can include the target’s thermal emission (which however is negligible for Ganymede up to 5 micrometers). In the Section [Photometric Correction](#Instrument,-Observations-and-Dataset/03_Photometric_correction.md) it will be explained how this correction was applied in the data. 


[^note2]: The data reduction routine transforms the raw observations into cleaned and calibrated data sets that are ready for scientific use. The reduction accounts for detector properties and observation conditions to ensure that observations are calibrated and compatible.

---









### NIMS errors

To estimate the NIMS instrument error of the mean spectrum, a coarse-grained procedure is used in order to obtain a spectrum of instrument errors associated with the radiance factor I/F, i.e., the error bars to be associated with the mean spectral profile. 

Because the variations in target spectral radiance of interest are often small in magnitude and are difficult to measure precisely, considerable interest is created in the definition and measurement of the capability of a sensor to respond to small spectral radiance changes. This capability, related to sensitivity, is commonly represented in the noise metric of the signal-to-noise ratio (SNR), but it is often described also in terms of **Noise Equivalent Spectral Radiance (NESR)**, which is the minimum detectable variation in spectral radiance. 
> The NESR of a scanner system is defined as _**the amount of change in spectral radiance required to produce a signal equivalent to the system’s noise**_. Stated another way, this is the quantity of spectral radiance that produces a signal-to-noise ratio of unity. An input radiance change that will produce an output signal equal to the noise (SNR = 1) is the minimum detectable change in radiance level. Thus, NESR is the minimum detectable radiance and is defined as the resolution of the system.

While computing an average spectrum, the standard deviation can be estimated as NESR/sqrt(n), where n is the number of independent spectra. After converting this value to I/F, it turns out that the standard deviation of the I/F is negligible with respect to systematic errors.

Import and read the homologous NIMS cube calibrated in units of spectral radiance (rather than in I/F units)
```Python
dataname3 = 'ERR_g1g002cr.qub.dat'
filename3 = 'ERR_g1g002cr.qub.dat.hdr'
openfile3 = open(filename3,'r')
readfile3 = openfile3.read()
print(readfile3) 
print('[...]') 
```
```Python
img3 = open_image('ERR_g1g002cr.qub.dat.hdr')    # (120,100,228)

# Imported binary data are loaded in 'data'
with open('ERR_g1g002cr.qub.dat', 'rb') as f:
    fileContent3 = f.read()    

# Convert binary data to float data
array3 = np.frombuffer(fileContent3, np.float32)

# Reshape the array
ERR_g1g002_raw = np.reshape(array3, [228, 120, 100])

# Plot
fig, ax = plt.subplots(figsize=(10,7))
hsi_image(ax, 'G1GNGLOBAL01A (0.7-$\mu m$ image)\n90°S to 70°N - 90° to 240°W', ERR_g1g002_raw, 0, 'Radiance $(W/m^2/sr/\mu m)$')
plt.show()
```
<img width="323" alt="Screenshot 2023-06-24 alle 23 21 53" src="https://github.com/gransss/Master_thesis/assets/136255551/61b111aa-182e-43d1-aa45-527ca5ff8009">

```Python
fig, ax2 = plt.subplots(figsize=(12,6))
ax2.set_ylim(0., 12)
ax2.set_xlim(0.7101,5.2365)
ax2.set_title("Spectral Radiance, range 0.7-5.2 $\mu m$\n Single Galileo/NIMS pixels | 12000 pixels")
ax2.set_ylabel("Spectral Radiance $(W/m^2/sr/\mu m)$", fontsize=10)
ax2.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
ax2.plot(wavel, np.reshape(ERR_g1g002_raw, (228,12000)), color='gray', linewidth=0.1, alpha=0.25)
ax2.grid(True, lw=0.5, ls='--', zorder=0, alpha=0.20)
plt.show()
#fig.savefig('procedureSpectrum.svg', transparent=True, format='svg', dpi=600)
```
<img width="721" alt="Screenshot 2023-06-24 alle 23 22 35" src="https://github.com/gransss/Master_thesis/assets/136255551/3b8c8cae-1e61-4ab4-908e-2e52001a48d9">

```Python
# Remove negative values
ERR_g1g002_raw_adj = np.where(ERR_g1g002_raw<0, 0, ERR_g1g002_raw)
ERR_g1g002_raw_adj_1 = ERR_g1g002_raw_adj.copy()
ERR_g1g002_raw_adj_1[ERR_g1g002_raw_adj_1 == 0.] = np.nan
print("The {} called 'ERR_g1g002_raw_adj' has the following shape"
       ": {}".format(g1g002_raw_adj.__class__,g1g002_raw_adj.shape))
```

Choice of the Regions of Interest of the sky background pixels
> Such pixels are representative of the instrumental noise linked to that specific acquisition, and particular carefulness was used to choose them far enough from the edge of the satellite, preferably in the vertical direction (above or below the disk) rather than on the sides, to avoid in-field straylight.

```Python
fig, (ax1,ax2) =plt.subplots(2,1, figsize=(14,8),sharex=True)
fig.subplots_adjust(hspace = 0.1)
ax1.imshow(ERR_g1g002_raw_adj_1[0,0:41,:], cmap=cm.turbo)
ax2.imshow(ERR_g1g002_raw_adj_1[0,80:120,:], cmap=cm.turbo)
ax1.set_yticks([0,10,20,30,40], labels=[0,10,20,30,40])
ax2.set_yticks([0,10,20,30,40], labels=[80,90,100,110,120])
ax2.set_xticks([0,25,50,75,100], labels=[0,25,50,75,100])
ax1.axvline(40,lw=0.5,c='white',ls='--')
ax1.axvline(45,lw=0.5,c='white',ls='--')
ax1.axhline(6,lw=0.5,c='white',ls='--')
ax1.axhline(11,lw=0.5,c='white',ls='--')
ax2.axvline(68,lw=0.5,c='white',ls='--')
ax2.axvline(71,lw=0.5,c='white',ls='--')
ax2.axhline(28,lw=0.5,c='white',ls='--')
ax2.axhline(30,lw=0.5,c='white',ls='--')
rect1 = patches.Rectangle((40,6), 5,5, linewidth=1, edgecolor='w', facecolor='none')
rect2 = patches.Rectangle((68,28),3,2, linewidth=1, edgecolor='w', facecolor='none')
ax1.add_patch(rect1)
ax2.add_patch(rect2)
plt.show()
```
<img width="560" alt="Screenshot 2023-06-24 alle 23 38 42" src="https://github.com/gransss/Master_thesis/assets/136255551/2d43c060-4ce1-4b81-8f35-a4c7a30b8441">

```Python
### SELECT A ROI ABOVE OR BELOW THE DISK
up = ERR_g1g002_raw_adj_1[:, 6:12,  40:45]       # 6,10,40,44 
up1 = np.mean(up)
down = ERR_g1g002_raw_adj_1[:, 108:110, 68:71]   # 108:111, 68:73  
down1 = np.mean(down)

fig,(ax1,ax2)=plt.subplots(1,2,figsize=(10,4))
ax1.set_title("ROI below the disk | # of pixels: {}".format(down.shape[1]*down.shape[2]))
plot1 = ax1.imshow(ERR_g1g002_raw_adj_1[0, 108:110, 68:71])
cbar1 = plt.colorbar(plot1, ax=ax1, shrink=0.5)
ax2.set_title("ROI above the disk | # of pixels: {}".format(up.shape[1]*up.shape[2]))
plot2 = ax2.imshow(ERR_g1g002_raw_adj_1[0, 6:12,  40:45])
cbar2 = plt.colorbar(plot2, ax=ax2, shrink=0.5)
plt.show()
```
<img width="596" alt="Screenshot 2023-06-24 alle 23 39 25" src="https://github.com/gransss/Master_thesis/assets/136255551/87f16890-9e90-4f85-8ace-a08127d4bb05">

Computation of the NESR (from the Region Of Interest below the disk)
> The standard deviation for the chosen ROI is computed, obtaining a sigma-value for each individual NIMS spectral channel, and is divided by the square root of the number of ROI pixels, i.e. the independent spectra. This is the NESR, i.e., the in-flight instrumental noise.
```Python
### COMPUTE THE STANDARD DEVIATION ALONG ALL THE SPECTRAL CHANNELS
up_std = np.nanstd(up, axis=(1,2), dtype=float) 
down_std = np.nanstd(down, axis=(1,2), dtype=float) 

### DIVIDE BY THE NUMBER OF PIXELS IN THE ROI
up_std = up_std / np.sqrt(up.shape[1]*up.shape[2])
down_std = down_std / np.sqrt(down.shape[1]*down.shape[2])

```Python
fig, ax2 =plt.subplots(figsize=(10,6))
ax2.plot(wavel,down_std,lw=1,color='snow')
ax2.set_title("NESR | Below the disk")
ax2.set_ylabel("Spectral Radiance $(W/m^2/sr/\mu m)$", fontsize=10)
ax2.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
ax2.grid(True, lw=0.5, ls='--', zorder=0, alpha=0.20)
ax2.text(4.5,0.0045,'NESR = $\\frac{\\sigma}{\\sqrt{N}}$',fontsize='x-large',fontweight='bold')
plt.show()
```
<img width="623" alt="Screenshot 2023-06-24 alle 23 41 35" src="https://github.com/gransss/Master_thesis/assets/136255551/a9b71baa-35f2-4197-a19c-35964c1b58a1">

Finally, the NESR spectrum is divided by the extra-terrestrial solar irradiance (also expressed in units of spectral radiance and convoluted for the NIMS wavelengths), scaled for the heliocentric distance of Ganymede, to obtain a I/F noise spectrum. The solar irradiance is based on combinations of measured and modeled data from multiple sources.

> Solar irradiance
```Python
solar = np.array([1345.1984,1264.4429,1281.2983,1275.1310,1234.0485,1199.9309,1171.8506,1133.1072,1097.5304,1062.0968,1028.4741,986.47144,
                  967.19940,956.50635,943.35730,925.26550,897.14484,871.74884,842.29150,820.77673,798.79163,779.48352,761.76367,743.93964,726.72205,
                  690.08923,652.53070,610.63098,578.21887,551.70166,526.70294,500.20569,478.08710,459.53619,437.60260,420.28864,414.84937,404.50330,
                  387.32391,370.43292,355.10468,338.51501,322.36789,309.05536,295.33261,284.39569,274.59531,272.22186,265.54877,261.01007,257.79529,
                  250.71153,246.35423,241.98517,237.45270,231.41800,226.27832,221.33755,214.07755,209.14163,204.94800,199.00485,191.87079,186.40695,
                  183.68881,180.10782,175.72684,171.28761,165.78198,160.30211,158.56613,157.23421,155.60797,153.77827,151.49872,145.94269,141.10229,
                  139.10114,137.29256,134.55106,130.18533,125.73938,123.73738,121.82857,119.26489,116.93965,114.61462,111.74739,108.96449,106.70959,
                  104.26938,101.89030,99.571045,97.425201,95.369499,94.183151,93.288536,90.021797,84.964005,82.507156,79.167297,75.760117,72.574829,
                  69.395630,66.269165,63.553379,60.828850,59.278946,58.579437,56.862930,54.520981,52.422047,50.698093,48.849144,46.924942,45.201729,
                  43.488159,41.620102,40.452534,39.408974,39.290558,38.088097,36.756218,35.333755,34.299980,33.152451,31.997486,30.778866,29.877087,
                  28.869406,27.935286,27.358711,27.062078,26.440956,25.553209,24.595772,23.952820,23.190716,22.434982,21.760132,21.096800,20.468430,
                  19.840672,19.344923,19.231518,18.728760,18.050718,17.594929,17.114887,16.595457,16.072327,15.679684,15.243275,14.798540,14.395081,
                  14.075068,13.976782,13.668694,13.294924,12.949822,12.576454,12.187355,11.811382,11.492685,11.095566,10.915661,10.647459,10.449915,
                  10.393959,10.197378,9.9198380,9.6677933,9.4382496,9.1787567,8.9388142,8.6912804,8.4876213,8.2185173,8.0838280,7.9481487,7.9048357,
                  7.7601442,7.5613689,7.3759751,7.1781974,7.0440979,6.8733454,6.6801300,6.4078083,6.2727046,6.0097189,5.9277897,5.8983059,5.7099962,
                  5.5974407,5.4505510,5.3195424,5.2051148,5.0637884,4.9791818,4.8374424,4.7670975,4.6455460,4.5807891,4.5696287,4.5264120,4.4306984,
                  4.3517275,4.2554541,4.1643386,4.0957346,4.0162687,3.9247775,3.8544331,3.7681692,3.7160499,3.6956410,3.6472192,3.5690308,3.5030036,
                  3.4428129,3.3681123,3.2981606,3.2325149,3.1805115,3.1237082,3.0584524,3.0161867])
```

> Spectrum of instrumental errors
```Python
NESR = down_std
SI_Kur = solar

IF_NESR = NESR / ((SI_Kur/(5.198**2)) / np.pi)    #[ (SI_Kur/(5.198^2)) / π ]
IF_NESR.shape
```

```Python
fig,ax=plt.subplots(figsize=(22,7))
ax.scatter(wavel, IF_NESR,color='snow',linewidths=0.1)
ax.grid(True, lw=0.5, ls='--', zorder=0, alpha=0.20)
ax.set_title('NIMS instrument error')
ax.set_ylabel("Radiance Factor I/F", fontsize=10)
ax.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
ax.text(0.55,0.0019,'Mean: {}\nMax: {}'.format(np.round(np.nanmean(IF_NESR),6), np.round(np.nanmax(IF_NESR),5)), fontsize='medium')
plt.show()
```
<img width="1147" alt="Screenshot 2023-06-24 alle 23 44 29" src="https://github.com/gransss/Master_thesis/assets/136255551/5458d203-2da5-489e-88b3-1681473b8a49">

```Python
fig, (ax1,ax2,ax3) =plt.subplots(1,3,figsize=(16,3.2))
fig.subplots_adjust(wspace = 0.15)
ax1.set_ylim(0.18,0.37)
ax1.set_xlim(0.7,1.5)
ax1.plot(wavel, np.reshape(np.ma.masked_array(g1g002_smooth, ~mask_i_3D), [228, 12000]), color='gray', linewidth=0.1, alpha=0.1)
ax1.errorbar(wavel, avg_spectrum_global_SM, yerr=IF_NESR*2, ecolor='snow', elinewidth=1.2, linewidth=0.6, color='snow', label='Mean spectrum')
ax1.set_ylabel("Radiance Factor I/F", fontsize=10)
#ax1.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
#ax1.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
ax2.set_ylim(0.05,0.225)
ax2.set_xlim(1.4,2.75)
ax2.plot(wavel, np.reshape(np.ma.masked_array(g1g002_smooth, ~mask_i_3D), [228, 12000]), color='gray', linewidth=0.1, alpha=0.1)         
ax2.errorbar(wavel, avg_spectrum_global_SM, yerr=IF_NESR*2, ecolor='snow', elinewidth=1.2, linewidth=0.6, color='snow', label='Mean spectrum')
ax2.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
ax3.set_ylim(0.005,0.055)
ax3.set_xlim(2.55,5.2365)
#ax3.yaxis.tick_right()
#ax3.yaxis.set_label_position("right")
ax3.plot(wavel, np.reshape(np.ma.masked_array(g1g002_smooth, ~mask_i_3D), [228, 12000]), color='gray', linewidth=0.1, alpha=0.1)
ax3.errorbar(wavel, avg_spectrum_global_SM, yerr=IF_NESR*2, ecolor='snow', elinewidth=1.2, linewidth=0.6, color='snow', label='Mean spectrum')
#ax3.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
ax1.grid(True, lw=0.5, ls='--', zorder=0, alpha=0.20)
ax2.grid(True, lw=0.5, ls='--', zorder=0, alpha=0.20)
ax3.grid(True, lw=0.5, ls='--', zorder=0, alpha=0.20)
ax1.legend(loc='upper right')
ax2.legend(loc='upper right')
ax3.legend(loc='upper right')
plt.suptitle('Instrument 1$\sigma$-error bars', y=0.97)
plt.show()
```
<img width="954" alt="Screenshot 2023-06-24 alle 23 45 05" src="https://github.com/gransss/Master_thesis/assets/136255551/a4a0538d-ca20-4798-bab3-0b74af70363b">

#### Photometric correction for i<90° data





---

### Description of Spectral Features


---

### Regions Of Interest

---

### Linear Mixture Spectral Modeling

---

### Compositional Maps

