The application of a photometric correction is an essential step in obtaining a map expressing the surface variability in terms of composition and physical state. A sphere of uniform albedo illuminated by a point-like source (i.e., the Sun) appears to have varying brightness, with brightness dropping off toward the edge of the disk. In fact, the brightness of an object depends on the intrinsic properties of the surface materials (composition, grain size, roughness, porosity etc.) and the surface shape, but _also_ on the geometry of illumination and observation. To compare surface spectral reflectance from one region to another observed under different geometries, and interpreting the composition based on laboratory measurements (taken at geometries different from the planetary observations), a pixel-by-pixel photometric correction is required to convert the calibrated radiance factor I/F into **spectral reflectance**. 

The correction takes the form:

`Corrected image = Image in units of I/F × Photometric model`

Several models have been developed: in this study we adapt the approach of Shkuratov et al. which uses the Akimov model to correct the radiance factor I/F in NIMS observations for photometric effects. The steps of the procedure are shown in the Figure:

<img width="539" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/0767543d-0f8b-486f-b136-a18c2293d4f9">

---

Photometric correction for **i<90°** data
> Incidence angle within [0, 90°], since the photometric correction makes sense just for the dayside (i<90°)

```Python
# 2D mask
mask_i = (INCIDENCE_ANGLES>0)&(INCIDENCE_ANGLES<90)&(LATITUDES>LAT_min)&(LATITUDES<LAT_max)
INCIDENCE_ANGLES_masked = np.ma.masked_array(INCIDENCE_ANGLES, ~mask_i)
EMISSION_ANGLES_masked = np.ma.masked_array(EMISSION_ANGLES, ~mask_i)
PHASE_ANGLE_masked = np.ma.masked_array(PHASE_ANGLE, ~mask_i)

# 3D mask
mask_i_3d = np.zeros(g1g002_raw.shape, dtype=bool)
for t in range(len(wavelength)):
    mask_i_3d[t,:,:] = mask_i
print("The now 3D mask has shape: ", mask_i_3d.shape)   # Output: (228, 120, 100)

# Plot the mask
fig, (ax1) = plt.subplots(figsize=(5,5))
line_sample(ax1, 'Mask for incidence angle i<90°\nWHITE: masked\nBLACK: unmasked')
ax1.imshow(~mask_i_3d[0], cmap=cm.gray)
plt.show()
```

<img width="273" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/4910517c-78ba-440b-bbbb-8810c372958f">

```Python
fig, (ax2,ax3) = plt.subplots(1,2,figsize=(10,5))
fig.subplots_adjust(wspace = 0.15)
geo_image(ax2, 'INCIDENCE ANGLE $i<90°$', INCIDENCE_ANGLES_masked, 16, INC_ANGLE_min, INC_ANGLE_max, cm.magma,'i (°)')
#ax2.scatter(74,62, s=1, color='gray',marker='s',label='subsolar point')
#ax2.legend()
geo_image(ax3, 'INCIDENCE ANGLE', INCIDENCE_ANGLES, 16, INC_ANGLE_min, INC_ANGLE_max, cm.magma, 'i (°)')
#ax3.scatter(74,62, s=1, color='gray',marker='s',label='subsolar point')
#ax3.legend()
plt.show()
```

<img width="542" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/6bd5bfe0-ba04-47d6-99f9-f33e0ce68779">

> Conversion of i, e, phi from **degrees** to **radians***
```Python
print('Incidence Angle i(°)<90°:  min {}°   max {}°'.format(np.ma.min(INCIDENCE_ANGLES_masked), np.ma.max(INCIDENCE_ANGLES_masked)))
print('Emission Angle e(°):       min {}    max {}°'.format(np.ma.min(EMISSION_ANGLES_masked), np.ma.max(EMISSION_ANGLES_masked)))
print('Phase Angle phi(°):        min {}    max {}°'.format(np.ma.min(PHASE_ANGLE_masked), np.ma.max(PHASE_ANGLE_masked)))

# Output:
# Incidence Angle i(°)<90°:  min 1.2951749563217163°   max 89.97915649414062°
# Emission Angle e(°):       min 1.3918390274047852    max 84.5864486694336°
# Phase Angle phi(°):        min 28.728519439697266    max 29.92171859741211°
```

```Python
rad_INCIDENCE_ANGLES = np.radians(INCIDENCE_ANGLES_masked)          # i 
rad_EMISSION_ANGLES = np.radians(EMISSION_ANGLES_masked)            # e
rad_PHASE_ANGLE = np.radians(PHASE_ANGLE_masked)                    # phi     
rad_i = np.ma.masked_invalid(rad_INCIDENCE_ANGLES)
rad_e = np.ma.masked_invalid(rad_EMISSION_ANGLES)
rad_phi = np.ma.masked_invalid(rad_PHASE_ANGLE)
```

```Python
print('Incidence Angle i(rad)<90°:   min {}    max {}'.format(np.ma.min(rad_i), np.ma.max(rad_i)))
print('Emission Angle e(rad):        min {}    max {}'.format(np.ma.min(rad_e), np.ma.max(rad_e)))
print('Phase Angle phi(rad):         min {}       max {}'.format(np.ma.min(rad_phi), np.ma.max(rad_phi)))

# Output:
# Incidence Angle i(rad)<90°:   min 0.022605067119002342    max 1.5704325437545776
# Emission Angle e(rad):        min 0.024292172864079475    max 1.4763120412826538
# Phase Angle phi(rad):         min 0.501407265663147       max 0.5222325325012207
```

```Python
fig, (ax1, ax2, ax3) = plt.subplots(1, 3, figsize=(15,7))
#fig.subplots_adjust(wspace = 0.08)
line_sample(ax1, 'Incidence Angle $i$')
plot1 = ax1.imshow(rad_i, cmap=cm.Spectral)
cont1 = ax1.contour(rad_i, 7, colors='black', alpha=0.8, linewidths=0.4)
#ax1.scatter(74,62, s=1, color='gray',marker='s',label='subsolar point')
#ax1.legend()

line_sample(ax2, 'Emission Angle $e$')
plot2 = ax2.imshow(rad_e, cmap=cm.Spectral)
cont2 = ax2.contour(rad_e, 7, colors='black', alpha=0.8, linewidths=0.4)

line_sample(ax3, 'Phase Angle $\phi$')
plot3 = ax3.imshow(rad_phi, cmap=cm.Spectral)
cont3 = ax3.contour(rad_phi, 6, colors='black', alpha=0.8, linewidths=0.4)

ax1.clabel(cont1, inline=True, fontsize=8)
ax2.clabel(cont2, inline=True, fontsize=8)
ax3.clabel(cont3, inline=True, fontsize=8)
cbar1 = plt.colorbar(plot1, ax=ax1, location='bottom',fraction=0.036, pad=0.1)
cbar2 = plt.colorbar(plot2, ax=ax2, location='bottom',fraction=0.036, pad=0.1)
cbar3 =plt.colorbar(plot3, ax=ax3, location='bottom',fraction=0.036, pad=0.1)
cbar1.ax.set_title('Radians (rad)', fontsize=10)
cbar2.ax.set_title('Radians (rad)', fontsize=10)
cbar3.ax.set_title('Radians (rad)', fontsize=10)
plt.show()
#fig.savefig('photomANGLES.svg', transparent=True, format='svg', dpi=600)
```

<img width="885" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/c794cdec-ff6f-4b7d-8b33-3c60f9a38194">

PHOTOMETRIC CORRECTION - **Akimov disk function**
* mu = cos(i)
* mu_0 = cos(e)
* phi = phase angle

The photometric correction can be described as a product of a disk function D(i,e,phi), describing how reflectance varies over the surface at a constant phase angle, and a phase function A(phi, lambda), describing the phase dependence of the surface reflectance. 

The **Akimov model** has the advantage of a disk function that minimizes limb brightening as the emission angle approaches 90°. The phase function A(phi,lambda) for the studied dataset is negligible due to the favorable geometry of illumination of the NIMS observation. In fact, the phase angle ∼ 30° is comparable to the illumination geometry of laboratory measurements used for the spectral unmixing. 


```Python
mu = []         # cos of incidence angle
mu_0 = []       # cos of emission angle
phi = []        # phase angle
for k in range(img2.nrows):
    for j in range(img2.ncols):
        a = np.ma.cos(rad_i[k,j])  # mu
        b = np.ma.cos(rad_e[k,j])  # mu_0
        c = rad_phi[k,j]           # phi
        mu.append(a)
        mu_0.append(b)
        phi.append(c)
mu = np.ma.reshape(np.ma.asarray(mu), (120,100))
mu_0 = np.ma.reshape(np.ma.asarray(mu_0), (120,100))
phi = np.ma.reshape(np.ma.asarray(phi), (120,100))
```
```Python
print('Shape of "incidence angle":  {}   |   Shape of "mu=cos(i)":    {}'.format(rad_i.shape, mu.shape))
print('Shape of "emission angle":   {}   |   Shape of "mu_0=cos(e)":  {}'.format(rad_e.shape, mu_0.shape))

# Output
# Shape of "incidence angle":  (120, 100)   |   Shape of "mu=cos(i)":    (120, 100)
# Shape of "emission angle":   (120, 100)   |   Shape of "mu_0=cos(e)":  (120, 100)
```

```Python
fig, (ax1,ax2,ax3) = plt.subplots(1,3,figsize=(15,5))
fig.subplots_adjust(wspace = 0.08)
line_sample(ax1, '$\mu = cos(i)$')
plot1 = ax1.imshow(mu, cmap=cm.Reds)
cont1 = ax1.contour(mu, 10, alpha=0.8, colors='black', linewidths=0.4)
ax1.scatter(74,62, s=1, color='black',alpha=0.8,marker='s',label='subsolar point')
ax1.legend()

line_sample(ax2, '$\mu_0 = cos(e)$')
plot2 = ax2.imshow(mu_0, cmap=cm.Reds)
cont2 = ax2.contour(mu_0, 10, alpha=0.8, colors='black', linewidths=0.4)

line_sample(ax3, 'Phase Angle $\phi$')
plot3 = ax3.imshow(phi, cmap=cm.Reds)
cont3 = ax3.contour(phi, 10, alpha=0.8, colors='black', linewidths=0.4)

ax1.clabel(cont1, inline=True, fontsize=8)
ax2.clabel(cont2, inline=True, fontsize=8)
ax3.clabel(cont3, inline=True, fontsize=8)
plt.colorbar(plot1, ax=ax1, shrink=0.4)
plt.colorbar(plot2, ax=ax2, shrink=0.4)
plt.colorbar(plot3, ax=ax3, shrink=0.4)
plt.show()
```
<img width="874" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/b4149dee-fadc-41f7-bc5f-2deaa0b335d8">

Akimov disk function:
<img width="417" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/ee5be628-0db0-4eb8-933f-9397b0a3817c">

where the **photometric latitude β** and **longitude γ** are photometric parameters calculated following Kreslavsky et al., 2000. The scattering plane is defined as the plane containing the source, surface point, and an observer. The photometric latitude is the angle between the surface normal and that plane. The photometric longitude is the angle in the scattering plane between the projection of the surface normal and the vector from the surface point to the observer. In particular: 
<img width="767" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/af0c5172-7b0c-4672-9701-525403e4f0cc">

```Python
# PHOTOMETRIC LONGITUDE γ
def photom_long(mu, mu_0, phi):
    photom_long = {}
    g = 'photometric longitude'
    photom_long[g] = []
    gamma = np.ma.arctan((mu/mu_0 - np.ma.cos(phi))/np.ma.sin(phi))  # in radians
    photom_long[g] = gamma
    return photom_long[g]

# PHOTOMETRIC LATITUTE β
def photom_lat(mu_0, gamma):
    photom_lat = {}
    b = 'photometric latitude'
    photom_lat[b] = []
    beta = np.ma.arccos(mu_0/np.ma.cos(gamma))  # in radians
    photom_lat[b] = beta
    return photom_lat[b]

# DISK FUNCTION
def disk_func(gamma, beta, phi):
    disk_func = {}
    d = 'disk function'
    disk_func[d] = []
    d_f = np.ma.cos(phi/2.)*np.ma.cos((np.pi/(np.pi-phi))*(gamma-(phi/2.)))*(np.ma.cos(beta))**(phi/(np.pi-phi))/np.ma.cos(gamma)
    disk_func[d] = d_f
    return disk_func[d]
```

```Python
gamma = photom_long(mu, mu_0, phi)
beta = photom_lat(mu_0, gamma)
akimov = disk_func(gamma, beta, phi)
print("The PHOTOMETRIC LONG (gamma) has:    min {}  |  max {}".format(np.ma.amin(gamma), np.ma.amax(gamma)))
print("***The range of the photometric longitude (arctan) has to be : [{}, {}]".format(-np.pi/2.,np.pi/2.))
print("\nThe PHOTOMETRIC LAT (beta) has:      min {}  |  max {}".format(np.ma.amin(beta), np.ma.amax(beta)))
print("***The range of the photometric latitude (arccos) has to be : [{}, {}]".format(0,np.pi))
print("\nThe AKIMOV DISK FUNCTION has:        min {} |  max {}".format(np.ma.amin(akimov), np.ma.amax(akimov)))

# Output
# The PHOTOMETRIC LONG (gamma) has:    min -1.0679517811310038  |  max 1.4761936100971846
# ***The range of the photometric longitude (arctan) has to be : [-1.5707963267948966, 1.5707963267948966]
#
# The PHOTOMETRIC LAT (beta) has:      min 0.01331546070410592  |  max 1.426414764597011
# ***The range of the photometric latitude (arccos) has to be : [0, 3.141592653589793]
#
# The AKIMOV DISK FUNCTION has:        min 0.000926328475053438 |  max 1.1576717031765476
```
```Python
fig, (ax1, ax2, ax3) = plt.subplots(1,3, figsize=(16,7))
fig.subplots_adjust(wspace = 0.1)
line_sample(ax1, 'Photometric Longitude $\\gamma$')
plot1 = ax1.imshow(gamma)
cont1 = ax1.contour(gamma, 8, alpha=0.8, colors='black', linewidths=0.4)

line_sample(ax2, 'Photometric Latitude $\\beta$')
plot2 = ax2.imshow(beta)
cont2 = ax2.contour(beta, 6, alpha=0.8, colors='black', linewidths=0.4)

line_sample(ax3, 'Akimov Disk Function $D(i,e,\phi)$')
plot3 = ax3.imshow(akimov)
cont3 = ax3.contour(akimov, 10, alpha=0.8, colors='black', linewidths=0.4)
circle1 = plt.Circle((50,60), 46.5, alpha=0.5, color='firebrick', linewidth=0.5, fill=False)
ax3.add_patch(circle1)

ax1.clabel(cont1, inline=True, fontsize=8)
ax2.clabel(cont2, inline=True, fontsize=8)
ax3.clabel(cont3, inline=True, fontsize=8)
plt.colorbar(plot1, ax=ax1, shrink=0.4)
plt.colorbar(plot2, ax=ax2, shrink=0.4)
plt.colorbar(plot3, ax=ax3, shrink=0.4)
plt.show()
```
<img width="919" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/b6d9cbdd-7457-4bcf-9c47-249861cc77af">

```Python
fig, ax3 = plt.subplots(figsize=(12,7))
line_sample(ax3, 'Akimov Disk Function $D(i,e,\phi)$')
plot3 = ax3.imshow(akimov)
cont3 = ax3.contour(akimov, 10, alpha=0.8, colors='black', linewidths=0.4)
circle1 = plt.Circle((50,60), 46.5, alpha=0.8, color='white', linewidth=0.4, ls='--', fill=False)
ax3.add_patch(circle1)
ax3.clabel(cont3, inline=True, fontsize=8)
cbar1 = plt.colorbar(plot3, ax=ax3, location='bottom',fraction=0.036, pad=0.1)
plt.show()
#fig.savefig('photomAKIMOV.svg', transparent=True, format='svg', dpi=600)
```
<img width="323" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/af387298-3a8a-4f0a-915b-2b683f129986">


> Photometrically adjusted data + negative values removed + smoothing
```Python
# Photometric correction
g1g002_akimov = g1g002_raw[:,...]*akimov   
# Remove negative values
g1g002_akimov_adj = np.where(g1g002_akimov<0, 0, g1g002_akimov)  

# Smoothing
g1g002_akimov_adj_smooth = sp.signal.savgol_filter(g1g002_akimov_adj, 27, 3)          
g1g002_akimov_adj_smooth = np.where(g1g002_akimov_adj_smooth<0, 0, g1g002_akimov_adj_smooth) 
```
```Python
fig, (ax1, ax2) = plt.subplots(1,2, figsize=(14,7))
fig.subplots_adjust(wspace = 0.15)
hsi_image(ax1, 'G1GNGLOBAL01A (0.7-$\mu m$ image)\nRadiance Factor $I/F$', np.ma.masked_array(g1g002_raw, ~mask_i_3D), 0, 'Radiance Factor I/F')
hsi_image(ax2, 'G1GNGLOBAL01A (0.7-$\mu m$ image)\nReflectance', np.ma.masked_array(g1g002_akimov, ~mask_i_3D), 0, 'Reflectance')
plt.show()
```
The photometric correction is then applied to the data cube to obtain the geometrically corrected radiance factor, or **reflectance**. A comparison between NIMS 0.7-micrometer image before and after the correction is shown in the Figure.
<img width="742" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/86b0fe57-9801-4c46-ab26-defa019069b7">

> Mean and median
```Python
avg_spectrum_akimov_adj_smooth = np.nanmean(np.ma.masked_array(g1g002_akimov_adj_smooth, ~mask_i_3D), axis=(1,2)) 
median_spectrum_akimov_adj_smooth = np.ma.median(np.ma.masked_array(g1g002_akimov_adj_smooth, ~mask_i_3D), axis=(1,2))

# SMOOTHED
avg_spectrum_akimov_adj_smooth = sp.signal.savgol_filter(avg_spectrum_akimov_adj_smooth, 7,2)         # smoothed mean
median_spectrum_akimov_adj_smooth = sp.signal.savgol_filter(median_spectrum_akimov_adj_smooth, 7,2)   # smoothed median
```

```Python
fig, ((ax1,ax2), (ax3,ax4)) = plt.subplots(2,2, figsize=(18,10), sharex=True, sharey=True)
fig.subplots_adjust(wspace = 0.1)
hsi_tot_spectrum(ax1, 'I/F spectra (original dataset)', g1g002_raw)
ax1.plot(wavel, avg_spectrum_global_SM, color='snow', linewidth=1, label='Mean')
ax1.plot(wavel, median_spectrum_global_SM, color='orangered', linewidth=1, label='Median')
ax1.legend(loc='upper right')
hsi_tot_spectrum(ax2, 'I/F spectra (photometrically corrected dataset)', g1g002_akimov)
ax2.set_ylabel('Reflectance')
hsi_tot_spectrum(ax3, 'I/F spectra (photometrically corrected dataset + adjusted)', g1g002_akimov_adj)
ax3.set_ylabel('Reflectance')
hsi_tot_spectrum(ax4, 'I/F spectra (photometrically corrected dataset + adjusted + smoothed)', g1g002_akimov_adj_smooth)
ax4.set_ylabel('Reflectance')
ax4.plot(wavel, avg_spectrum_global_SM, color='snow', linewidth=1, alpha=0.35, label='Mean')
ax4.plot(wavel, avg_spectrum_akimov_adj_smooth, color='snow', linewidth=1, alpha=1, label='Mean (Akimov)')
ax4.plot(wavel, median_spectrum_global_SM, color='orangered', linewidth=1, alpha=0.35, label='Median')
ax4.plot(wavel, median_spectrum_akimov_adj_smooth, color='orangered', linewidth=1, alpha=1, label='Median (Akimov)')
ax4.legend(loc='upper right')
plt.show()
```
<img width="1054" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/e8410faf-fa9f-48f4-93f9-4df51cbe809e">

```Python
fig,ax2 = plt.subplots(figsize=(11,8))   #(18,20)
ax2.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
ax2.set_ylabel('Reflectance', fontsize=10)
ax2.set_ylim(0., 0.8)
ax2.set_xlim(0.7101,5.2365)
ax2.plot(wavel, np.reshape(np.ma.masked_array(g1g002_akimov_adj_smooth, ~mask_i_3D), [228, 12000]), color='gray', linewidth=0.1, alpha=0.25)
ax2.grid(True, lw=0.5, zorder=0, alpha=0.20)
ax2.set_title('Reflectance (photometrically corrected) spectra, single Galileo/NIMS pixels | 12000 pixels')
ax2.plot(wavel, avg_spectrum_global_SM, color='snow', linewidth=1, ls='--', alpha=0.5, label='Mean (before)')
ax2.plot(wavel, median_spectrum_global_SM, color='orangered', linewidth=1, ls='--', alpha=0.5, label='Median (before)')
ax2.plot(wavel, avg_spectrum_akimov_adj_smooth, color='snow', linewidth=1, alpha=1, label='Mean (after)')
ax2.plot(wavel, median_spectrum_akimov_adj_smooth, color='orangered', linewidth=1, alpha=1, label='Median (after)')
ax2.legend(frameon=False)

axins = inset_axes(ax2, width=1.8, height=2.4, loc='center right', borderpad=2)
axins.set_xlim(0, 99)
axins.set_ylim(119, 0)
major_ticksx = (0,25,50,75,100)
minor_ticksx = np.arange(0, 100, 1)
major_ticksy = (0,25,50,75,100)
minor_ticksy = np.arange(0, 120, 1)
axins.set_xticks(major_ticksx)
axins.set_xticks(minor_ticksx, minor=True)
axins.set_yticks(major_ticksy)
axins.set_yticks(minor_ticksy, minor=True)
axins.imshow(mask_i_3D[0], cmap=cm.gray)   # ~
circle = plt.Circle((50,60), 46.5, alpha=0.5, color='white', ls='--', linewidth=0.5, fill=False)
axins.add_patch(circle)

plt.show()
fig.savefig('photomSPECTRUM.svg', transparent=True, format='svg', dpi=600)
```
<img width="662" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/0adf0dff-3737-44d9-a0cf-2c39b3bb6544">
