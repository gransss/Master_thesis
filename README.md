# Master_thesis
_Title_ Surface composition of Jupiter’s icy moon Ganymede: clues about habitability through **spectral unmixing** technique.

## Table of contents
* [Introduction](#Introduction)
* [Spectral Unmixing](#Spectral_unmixing)


## Introduction
**_Context_**. The study of astrobiologically compelling targets is critical if we are to understand biology as a potentially common phenomenon in our Galaxy and beyond. Liquid water, a keystone of habitable environments for life as we know it, was discovered to be hosted as a global ocean beneath Ganymede’s icy crust, providing conceptual basis within which new theories on habitability can be constructed.

_**Aims**_. Investigation of Ganymede’s surface composition, that is presented in this work, could provide a window into the habitability of its oceans. Following a computational approach well-established within the remote-sensing scientific community, we derive our study from a _linear mixing_ modeling of a hyperspectral mosaic acquired by the Near-Infrared Mapping Spectrometer (NIMS), onboard the NASA Galileo spacecraft (1995-2003). We aim at deriving abundance maps of constituent spectral endmembers, related to pure species measured in the laboratory, under the assumption that they combine linearly to produce the surface’s measured spectral profiles.

_**Methods**_. The Galileo/NIMS mosaic, in units of calibrated radiance factor (I/F), is imported, processed and photometrically corrected. By exploiting a library of reference spectral endmembers measured in the laboratory, an unmixing model is implemented through a fully constrained least squares minimization (FCLS) algorithm, applied to a subset of spectra representative of selected regions of interest on the surface. The spectra are iteratively modeled with different combinations of endmembers, the best of which provides abundance values and related maps describing the chemical composition of the uppermost millimeter-thick surface layer.
	
## Spectral Unmixing
We introduce the hyperspectral dataset from the Galileo/NIMS instrument. Data are read, displayed and processed in Python. Descriptions of selected spectra from specific regions of interest, well-representative of the spectral diversity of the surface, are thus provided. This set of spectra is the one given to the linear spectral mixing model for abundance retrieval. We will rely on an existing database of laboratory reference spectra, acquired at a temperature representative of the studied surface, to decompose the measured spectral profiles into a limited number of components and their relative abundances. Finally, we present best-estimate abundance values of the different endmembers identified by the model, providing maps describing the chemical composition of the uppermost millimeter-thick surface layer. 

### Pipeline
* [Instrument, Observations and Dataset](#Instrument_obs_dataset)
* [Description of Spectral Features](#Spectral_features)
* [Regions Of Interest](#ROI)
* [Linear Mixture Spectral Modeling](#Modeling)
* [Compositional Maps](#Maps)

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

### Dataset G1GNGLOBAL01A1
The NIMS instrument is a whiskbroom-type scanner, therefore imaging is performed by a combination of one-dimensional mirror spatial scanning, coupled with orthogonal spacecraft scan-platform motion, yielding two-dimensional images for each of the NIMS wavelengths. The spectral coverage is 0.7 to 5.2 micrometers: this spectral region includes the reflected-sunlight and thermal-radiation regimes for many Solar System objects, containing diagnostic spectral signatures arising from molecular vibrational transitions (and some electronic transitions) of both solid and gaseous species. The instrument’s Instantaneous Field Of View (IFOV) is 0.5 mrad, giving a spatial resolution of, for example, about 5 km at 10000 km distance.

The data are encoded into 10-bit data numbers, formatted, usually written to the spacecraft tape recorder, and later transmitted to Earth. After receipt on the ground, the data are decompressed, nominally calibrated and geometrically corrected, and formatted into image cubes for analysis. The NIMS observation named **G1GNGLOBAL01A1** is used in this study. It refers to the first Galileo close encounter with Ganymede, with observation covering Ganymede’s anti-Jovian hemisphere centered near (10°S, 180°W), at 228 wavelengths and an approximate spatial resolution of 100 km per pixel. 

The natural output of an imaging spectrometer is a **hyperspectral data cube**, enabling the data to be analyzed spatially (an image at a time) or spectrally (a spectrum at a time). The structure of NIMS spectral image cubes follows Planetary Data System _qube_-object standards. The core of the qube is a 3-dimensional array of 32-bit VAX floating-point pixels (in spectral radiance or I/F units) normally arranged in band sequential (BSQ)[^note] order: sample, line and band. The core is followed by a set of backplanes (or _extra_ bands) with 32-bit VAX floating-point pixels, containing a number of geometric parameters, native time, projected line and sample and spectral index bands, each a user-specified function of the data bands. The geometric backplanes include latitude, longitude, incidence, emission and phase angles, slant distance and intercept altitude.

| Name        | Description      |
| :------------- |:------------- | 
| g1g002ci.LBL      | Text file containing a description of the data and the associated labels, following the standards of the Planetary Data System |
| g1g002ci.qub.dat      | Data cube calibrated in radiance factor also known as **I/F**, which is the ratio of the measured radiance to that of a Lambertian surface illuminated and viewed from the normal direction, at the same distance from the Sun.  |  
| g1g002ci.qub.dat.hdr |   Header information relative to the file _g1g002ci.qub.dat_ |   
| g1g002ci.qub.geo | File containing the so called 'backplanes', concerning geometrical parameters of the planetary observation. |
| g1g002ci.qub.geo.hdr | Header information relative to the file _g1g002ci.qub.geo_|

The NIMS data cube for the dataset **G1GNGLOBAL01A1** was available in its reduced form[^note2], along with all the observation geometry parameters needed for our analysis. The cube includes a total of 228 × 120 × 100 pixels calibrated in radiance factor r_F = \pi*r where the r is the bidirectional reflectance of the surface. The _calibrated_ radiance factor, indicated with I/F, is the ratio between the radiance measured by the instrument on the target and the solar radiance scaled for the heliocentric distance of the target expressed in AU. In this way, the dimensionless quantity I/F describes the reflectivity of a target as a function of wavelength, having automatically excluded the spectral signatures of solar nature. 

[^note]: Band sequential (BSQ), together with band interleaved by line (BIL) and band interleaved by pixel (BIP), are three methods of organizing image data for multiband images. BIL, BIP, and BSQ are not in themselves image formats but are schemes for storing the actual pixel values of an image in a binary file. In particular, band sequential format stores information for the image one band at a time: data for all the pixels for band 1 is stored first, then data for all pixels for band 2, and so on.

[^note2]: The data reduction routine transforms the raw observations into cleaned and calibrated data sets that are ready for scientific use. The reduction accounts for detector properties and observation conditions to ensure that observations are calibrated and compatible.






#### File g1g002ci.qub.dat

This is the core of the qube, a 3-dimensional array of 32-bit VAX floating-point pixels (in spectral radiance or I/F units) arranged in band sequential (BSQ)[^note] order: sample, line and band. The cube includes a total of **228 × 120 × 100 pixels** calibrated in radiance factor r_F = \pi*r where the r is the bidirectional reflectance of the surface. The _calibrated_ radiance factor, indicated with **I/F**, is the ratio between the radiance measured by the instrument on the target and the solar radiance scaled for the heliocentric distance of the target expressed in AU. In this way, the dimensionless quantity I/F describes the reflectivity of a target as a function of wavelength, having automatically excluded the spectral signatures of solar nature. 

> Import and read the file `g1g002ci.qub.dat`:

```Python
dataname = 'g1g002ci.qub.dat'
filename = 'g1g002ci.qub.dat.hdr'
openfile = open(filename,'r')
readfile = openfile.read()
print(readfile)  # To print just a section of file -> readfile[:XXX], with XXX=number of characters to print
print('[...]') 
```

```Python
img = open_image('g1g002ci.qub.dat.hdr')  
print("Class of the file 'img':         ", img.__class__)   # Output: <class 'spectral.io.bsqfile.BsqFile'>
print("The shape of 'img' is:           ", img.shape)  # Output: Size (120,100,228)          
print("Bands x Rows x Columns:          ", img.nbands*img.nrows*img.ncols)    # Output: 2736000
print("Bands x Rows x Columns x 4 bits: ", img.nbands*img.nrows*img.ncols*4)  # Output: 10944000
print('\n',img)
```

> The file contains the values of the wavelenghts used for the sampling, `wavelength = {0.710100, 0.723100, 0.736100, ...}`.
> Read the NIMS wavelenght values from the data:

```Python
wavelength = img.metadata['wavelength']
print("The length of the wavelengths list is: ",len(wavelength))   # Output: 228 
wavel_ = [float(i) for i in wavelength]
wavel__ = np.array(wavel_, dtype=np.float32)
wavel = np.sort(wavel__)
```

> Define the main spectral features in the spectral range:
```Python
sp_feat = [1.04, 1.25, 1.50, 1.57, 1.65, 2.02, 3.1, 3.6, 4.25]  # Values in micrometers
idxs = []   # Respective indexes of the selected values
for i in range(len(sp_feat)):
    idx = find_nearest_228(wavel,sp_feat[i])[1]
    idxs.append(idx)
idxs = np.array(idxs)
idxs   # Output: array([ 25,  34,  44,  50,  56,  87, 137, 159, 186])
```

Data are read from binary format (class 'bytes') to float32 (float with 32 bits)
```Python
with open('g1g002ci.qub.dat', 'rb') as f:
    fileContent = f.read()  
print("The file class is: ", type(fileContent))  # Output: <class 'bytes'>
print("The number of elements in the file is (Bands x Rows x Columns x 4 bits): ",len(fileContent))  # Output: 10944000

# the function np.frombuffer() interprets a buffer as a 1-dimensional array
array = np.frombuffer(fileContent, np.float32)
array = array*100
print("\nThe {} object called 'array' has the following features:\n"   # Output: <class 'numpy.ndarray'> 
       "Dimensions of the array: {}\n"  # Output: 1
       "Total number of elements: {}\n"  # OUtput: 2736000
       "# elements stored in each dimension: {}\n"   # Output: (2736000,)
       "Data type object: {}".format(array.__class__, array.ndim, array.size, array.shape, array.dtype))   # Output: float32
```

> Reshape the 1D array into a 3D matrix of dimensions equal to the data cube (228 bands, 120 lines, 100 samples)
```Python
g1g002_raw = np.reshape(array, [228, 120, 100])
print("The {} called 'g1g002_raw' has the following features:\n\n"   # Output: <class 'numpy.ndarray'> 
       "Dimensions of the array: {}\n"   # Output: 3
       "Total number of elements: {}\n"  # Output: 2736000
       "# elements stored in each dimension (Bands x Rows x Cols): {}\n"   # Output: (228, 120, 100)
       "Data type object: {}".format(g1g002_raw.__class__, g1g002_raw.ndim, g1g002_raw.size, g1g002_raw.shape, g1g002_raw.dtype))  # Output: float32
print("The total number of pixels is (120x100): ",img.nrows*img.ncols)   # Output: 12000
print("The total number of bands is: ",img.nbands)    # Output: 228
```

#### File g1g002ci.qub.geo
This file includes a set of backplanes (or _extra_ bands) with 32-bit VAX floating-point pixels, containing a number of geometric parameters, native time, projected line and sample and spectral index bands, each a user-specified function of the data bands. The geometric backplanes include latitude, longitude, incidence, emission and phase angles, slant distance and intercept altitude.

> Import and read the file `g1g002ci.qub.geo`:

```Python
dataname2 = 'g1g002ci.qub.geo'
filename2 = 'g1g002ci.qub.geo.hdr'
openfile2 = open(filename2,'r')
readfile2 = openfile2.read()
print(readfile2)
```
```Python
img2 = open_image('g1g002ci.qub.geo.hdr')          
print(img2)
```

> Data are read from binary format (class 'bytes') to float32 (float with 32 bits)
```Python
with open('g1g002ci.qub.geo', 'rb') as f:
    fileContent2 = f.read()  
print("The file class is: ", type(fileContent2))  # Output: <class 'bytes'>
print("The number of elements in the file is (Bands x Rows x Columns x 4 bits): ",len(fileContent2))   # Output: 432000

array2 = np.frombuffer(fileContent2, np.float32)
print("\nThe {} object called 'array2' has the following features:\n"   # Output: <class 'numpy.ndarray'>
       "Dimensions of the array: {}\n"    # Output: 1
       "Total number of elements: {}\n"   # Output: 108000
       "# elements stored in each dimension: {}\n"    # Output: (108000,)
       "Data type object: {}".format(array2.__class__, array2.ndim, array2.size, array2.shape, array2.dtype))    # Output: float32
```

> Reshape the 1D array into a cube of (9 bands, 120 lines, 100 samples)
```Python
g1g002_geo = np.reshape(array2, [9, 120, 100])
print("The {} called 'g1g002_geo' has the following features:\n\n"   # Output: <class 'numpy.ndarray'>
       "Dimensions of the array: {}\n"   # Output: 3
       "Total number of elements: {}\n"  # Output: 108000 
       "# elements stored in each dimension (Bands x Rows x Cols): {}\n"    # Output: (9, 120, 100)
       "Data type object: {}".format(g1g002_geo.__class__, g1g002_geo.ndim, g1g002_geo.size, g1g002_geo.shape, g1g002_geo.dtype))   # Output: float32
```

> Stock each of the 9 backplanes in separate arrays
```Python
print(img2.metadata['band names'])
LATITUDES = g1g002_geo[0,:,:]
LONGITUDES = g1g002_geo[1,:,:]
INCIDENCE_ANGLES = g1g002_geo[2,:,:]
EMISSION_ANGLES = g1g002_geo[3,:,:]
PHASE_ANGLE = g1g002_geo[4,:,:]
SLANT_DISTANCES = g1g002_geo[5,:,:]
INTERCEPT_ALTITUDES = g1g002_geo[6,:,:]
PHASE_ANGLE_STD_DEVS = g1g002_geo[7,:,:]
SPECTRAL_RADIANCE_STD_DEVS = g1g002_geo[8,:,:]
```

> Latitude and Longitude
```Python
# Find max and min latitude values
LAT_min = np.nanmin(LATITUDES[LATITUDES != -np.inf]) 
LAT_max = np.amax(LATITUDES)
print("The max latitude value is: ", LAT_max)
print("The min latitude value is: ", LAT_min)

# Find max and min longitude values
LONG_min = np.nanmin(LONGITUDES[LONGITUDES != -np.inf]) 
LONG_max = np.amax(LONGITUDES)
print("\nThe max longitude value is: ", LONG_max)
print("The min longitude value is: ", LONG_min)
```
```Python
plt.style.use('dark_background')
fig, (ax1, ax2, ax3) = plt.subplots(1,3, figsize=(14,8))
#fig.subplots_adjust(wspace = 0.15)
geo_image(ax1, 'Latitude', LATITUDES, 18, LAT_min, LAT_max, cm.Spectral, 'Degrees (°)')     
geo_image(ax2, 'Longitude', LONGITUDES, 19, LONG_min, LONG_max, cm.Spectral, 'Degrees (°)') 
fig.delaxes(ax3)
plt.show()
```

<img width="550" alt="Screenshot 2023-06-24 alle 22 54 56" src="https://github.com/gransss/Master_thesis/assets/136255551/3d749278-5971-4855-8e84-12265fc5a5a7">

> Set a mask based on LAT and LONG data (useful for subsequent analysis)
```Python
mask_L = (LATITUDES>LAT_min)&(LATITUDES<LAT_max)
mask_L_3D = np.zeros(g1g002_raw.shape, dtype=bool)
for t in range(len(wavelength)):
    mask_L_3D[t,:,:] = mask_L
    
# Plot the mask
fig, (ax1) = plt.subplots(figsize=(5,5))
line_sample(ax1, 'Mask of the whole disk\nWHITE: masked\nBLACK: unmasked')
ax1.imshow(~mask_L_3D[0], cmap=cm.gray)
circle = plt.Circle((50,60), 46.5, alpha=0.4, color='black', ls='--', linewidth=0.5, fill=False)
ax1.add_patch(circle)
plt.show()
```

<img width="275" alt="Screenshot 2023-06-24 alle 22 55 18" src="https://github.com/gransss/Master_thesis/assets/136255551/b82423d9-31c5-4546-be7b-cbb1647146d7">

> Incidence angle, emission angle, and phase angle
```Python
# Find max and min incidence angle values
INC_ANGLE_min = np.nanmin(INCIDENCE_ANGLES[INCIDENCE_ANGLES != -np.inf]) 
INC_ANGLE_max = np.amax(INCIDENCE_ANGLES)
print("The max incidence angle value is: ",INC_ANGLE_max)
print("The min incidence angle value is: ",INC_ANGLE_min)

# Find max and min emission angle values
EMIS_ANGLE_min = np.nanmin(EMISSION_ANGLES[EMISSION_ANGLES != -np.inf]) 
EMIS_ANGLE_max = np.amax(EMISSION_ANGLES)
print("\nThe max emission angle value is: ",EMIS_ANGLE_max)
print("The min emission angle value is: ",EMIS_ANGLE_min)

# Find max and min phase angle values
PHASE_min = np.nanmin(PHASE_ANGLE[PHASE_ANGLE != -np.inf]) 
PHASE_max = np.amax(PHASE_ANGLE)
print("\nThe max phase angle value is: ",PHASE_max)
print("The min phase angle value is: ",PHASE_min)
```

```Python
fig, (ax1, ax2, ax3) = plt.subplots(1,3, figsize=(14,7))
fig.subplots_adjust(wspace = 0.15)
geo_image(ax1, 'Incidence Angle $i$', np.ma.masked_array(INCIDENCE_ANGLES, ~mask_L), 16, INC_ANGLE_min, INC_ANGLE_max, cm.Spectral,'Degrees (°)')
ax1.scatter(74,62, s=1, color='black', marker='s', label='subsolar point')
geo_image(ax2, 'Emission Angle $e$', np.ma.masked_array(EMISSION_ANGLES, ~mask_L), 12, EMIS_ANGLE_min, EMIS_ANGLE_max, cm.Spectral, 'Degrees (°)')
geo_image(ax3, 'Phase Angle $\phi$', np.ma.masked_array(PHASE_ANGLE, ~mask_L), 8, PHASE_min, PHASE_max, cm.Spectral, 'Degrees (°)')
ax1.legend()
plt.show()
```

<img width="826" alt="Screenshot 2023-06-24 alle 22 57 47" src="https://github.com/gransss/Master_thesis/assets/136255551/aabffe20-4de8-428b-95c0-30e8b5a3bb8c">


#### Radiance factor data analysis
All the data cubes have been calibrated in radiance factor r_F = \pi*r where the r is the bidirectional reflectance of the surface. The _calibrated_ radiance factor, indicated with **I/F**, is the ratio between the radiance measured by the instrument on the target and the solar radiance scaled for the heliocentric distance of the target expressed in AU. In this way, the dimensionless quantity I/F describes the reflectivity of a target as a function of wavelength, having automatically excluded the spectral signatures of solar nature. 

```Python
x0, y0, r0 = 50. ,60. ,46.5
fig, (ax1, ax2) = plt.subplots(1,2, figsize=(18,6))
fig.subplots_adjust(wspace = 0.07)
hsi_image(ax1, 'G1GNGLOBAL01A (0.7-$\mu m$ image)\n90°S to 70°N - 90° to 240°W', g1g002_raw, 0, 'Radiance Factor I/F')
hsi_tot_spectrum(ax2, 'Radiance Factor spectra, range 0.7-5.2 $\mu m$\n Single Galileo/NIMS pixels | 12000 pixels', g1g002_raw)
plt.show()
```
<img width="917" alt="Screenshot 2023-06-24 alle 23 00 52" src="https://github.com/gransss/Master_thesis/assets/136255551/45affe85-7415-4d6d-b211-0fd09110cc8a">

> Adjust the data
```Python
# Remove negative values
g1g002_raw_adj = np.where(g1g002_raw<0, 0, g1g002_raw)
g1g002_raw_adj_1 = g1g002_raw_adj.copy()
g1g002_raw_adj_1[g1g002_raw_adj_1 == 0] = np.nan
print("The {} called 'g1g002_raw_adj' has the following shape"   # Output: (228, 120, 100)
       ": {}".format(g1g002_raw_adj.__class__,g1g002_raw_adj.shape))

# MASK FOR i,e BELONGING TO THE DAYSIDE OF GANYMEDE
mask_i = (INCIDENCE_ANGLES>0)&(INCIDENCE_ANGLES<90)&(LATITUDES>LAT_min)&(LATITUDES<LAT_max)
mask_i_3D = np.zeros(g1g002_raw.shape, dtype=bool)
for t in range(len(wavelength)):
    mask_i_3D[t,:,:] = mask_i
    
# Plot the mask
fig, (ax1) = plt.subplots(figsize=(5,5))
line_sample(ax1, 'Dayside of Ganymede\n(WHITE: masked | BLACK: unmasked)')
ax1.imshow(~mask_i_3D[0], cmap=cm.gray)
circle = plt.Circle((50,60), 46.5, alpha=0.4, color='black', ls='--', linewidth=0.5, fill=False)
ax1.add_patch(circle)
plt.show()
```
<img width="275" alt="Screenshot 2023-06-24 alle 23 02 16" src="https://github.com/gransss/Master_thesis/assets/136255551/7295b356-40cc-43a5-86e4-8e7f5ff393eb">

```Python
fig, (ax1, ax2) = plt.subplots(1,2, figsize=(14,7))
fig.subplots_adjust(wspace = 0.1)
hsi_image(ax1, 'G1GNGLOBAL01A (0.7-$\mu m$ image)\n90°S to 70°N - 90° to 240°W', g1g002_raw, 0, 'Radiance Factor I/F')
hsi_image(ax2, 'G1GNGLOBAL01A, adjusted (0.7-$\mu m$ image)\n90°S to 70°N - 90° to 240°W', g1g002_raw_adj, 0, 'Radiance Factor I/F')
plt.show()
```
<img width="731" alt="Screenshot 2023-06-24 alle 23 03 22" src="https://github.com/gransss/Master_thesis/assets/136255551/70014295-ed4a-47b7-9a07-302f4f8f8726">

```Python
# Compute the mean and median along axes (1,2)
avg_spectrum_global = np.nanmean(np.ma.masked_array(g1g002_raw_adj, ~mask_i_3D), axis=(1,2)) 
median_spectrum_global = np.ma.median(np.ma.masked_array(g1g002_raw_adj, ~mask_i_3D), axis=(1,2)) 
```

```Python
fig, (ax1, ax2) = plt.subplots(1,2, figsize=(18,5))
ax1.set_ylim(-0.2, 0.8)
hsi_tot_spectrum(ax1, 'Radiance Factor spectra (original dataset), range 0.7-5.2 $\mu m$\n Single Galileo/NIMS pixels | 12000 pixels', g1g002_raw)

ax2.set_ylim(-0.2, 0.8)
hsi_tot_spectrum(ax2, 'Radiance Factor spectra (adjusted dataset), range 0.7-5.2 $\mu m$\n Single Galileo/NIMS pixels | 12000 pixels', np.ma.masked_array(g1g002_raw_adj, ~mask_i_3D))
#ax2.errorbar(wavel, avg_spectrum_global, yerr=std_spectrum_global, elinewidth=0.3, color='gold', linewidth=1, label='Mean spectrum')
ax2.plot(wavel, avg_spectrum_global, color='snow', linewidth=1, label='Mean')
ax2.plot(wavel, median_spectrum_global, color='orangered', linewidth=1, label='Median')
ax2.legend()
plt.show()
```
<img width="1065" alt="Screenshot 2023-06-24 alle 23 04 12" src="https://github.com/gransss/Master_thesis/assets/136255551/10635d2e-b901-4d7d-8e71-5aaf37b72375">

> Smoothing of the data (through **Savitzky-Golay** filter)
```Python
g1g002_smooth = sp.signal.savgol_filter(g1g002_raw, 7, 3)           # smoothed data
g1g002_smooth = np.where(g1g002_smooth<0, 0, g1g002_smooth)         # remove negative values

# Compute the SMOOTHED mean and median along axes (1,2)
avg_spectrum_global_SM = sp.signal.savgol_filter(avg_spectrum_global, 7,2)  
median_spectrum_global_SM = sp.signal.savgol_filter(median_spectrum_global, 7,2)
```

```Python
fig, (ax1, ax2) = plt.subplots(1,2, figsize=(18,5))
ax1.set_ylim(-0.2, 0.8)
hsi_tot_spectrum(ax1, 'Radiance Factor spectra (original dataset), range 0.7-5.2 $\mu m$\n Single Galileo/NIMS pixels | 12000 pixels', g1g002_raw)
ax2.set_ylim(0., 0.8)
ax2.set_xlim(0.7101,5.2365)
hsi_tot_spectrum(ax2, 'Radiance Factor spectra (smoothed dataset), range 0.7-5.2 $\mu m$\n Single Galileo/NIMS pixels | 12000 pixels', g1g002_smooth)
ax2.plot(wavel, avg_spectrum_global_SM, color='snow', linewidth=1, label='Mean')
ax2.plot(wavel, median_spectrum_global_SM, color='orangered', linewidth=1, label='Median')
ax2.legend(frameon=False)
plt.show()
```
<img width="1064" alt="Screenshot 2023-06-24 alle 23 05 27" src="https://github.com/gransss/Master_thesis/assets/136255551/d4fbdd4e-8869-4be7-a215-fa8cfd17221d">

```Python
fig, (ax1, ax2) = plt.subplots(1,2, figsize=(21,6))
fig.subplots_adjust(wspace = -0.05)
hsi_image(ax1, 'G1GNGLOBAL01A (0.7-$\mu m$ image)', g1g002_raw_adj, 0, 'I/F')
circle = plt.Circle((50,60), 46.5, alpha=0.4, color='white', ls='--', linewidth=0.5, fill=False)
ax1.add_patch(circle)
ax2.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
ax2.set_ylabel('Radiance Factor $I/F$', fontsize=10)
ax2.set_ylim(0., 0.75)
ax2.set_xlim(0.7101, 5.2365)
#ax2.yaxis.tick_right()
#ax2.yaxis.set_label_position("right")
ax2.plot(wavel, np.reshape(g1g002_smooth, [228, 12000]), color='gray', linewidth=0.1, alpha=0.25)
ax2.grid(True, lw=0.5, zorder=0, alpha=0.20)
ax2.set_title('Radiance factor spectra, range 0.7-5.2 $\mu m$\n Single Galileo/NIMS pixels | 12000 pixels')
#ax2.errorbar(wavel, avg_spectrum_filt_corr, yerr=std_spectrum_filt_corr, color='gold', ecolor='gold', elinewidth=0.5, barsabove=False, linewidth=1, label='Mean (Akimov, 0°<i<60°, 0°<e<60°)')
ax2.plot(wavel, avg_spectrum_global_SM, color='snow', linewidth=1, label='Mean')
ax2.plot(wavel, median_spectrum_global_SM, color='orangered', linewidth=1, label='Median')
ax2.legend(frameon=False)
plt.show()
```
<img width="1027" alt="Screenshot 2023-06-24 alle 23 06 13" src="https://github.com/gransss/Master_thesis/assets/136255551/288c3398-e1e6-4057-ab7a-5c1c5819052d">



