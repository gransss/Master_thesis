### Regions of interest (ROIs)

Informations about the geomorphological features of Ganymede can be found [here](https://planetarynames.wr.usgs.gov/Page/GANYMEDE/target).

```Python
# """" DICTIONARY WITH THE GEOGRAPHIC AND PIXEL COORDINATES OF THE SELECTED ROIs """"
ROI = {}
n = 'names'
geo = 'geographic coordinates'
yx = 'yx'
ROI[n] = ['Osiris crater','Menhit crater','Memphis Facula','Uruk Sulcus','Sippar Sulcus','Galileo Regio','Marius Regio']
ROI[geo] = [(-38,166.31),(-36.31,140.32),(15,134),(0.8,160.3),(-15.4,189.3),(30,160),(2.5,187.7)]  # geographical coordinates    14.1,131.9
ROI[yx] = []    # pixel coordinates                                                                       
for i in range(len(ROI[geo])):
    y,x = find_YX_from_LatLon(ROI[geo][i][0], ROI[geo][i][1])
    ROI[yx].append((y,x))   

# """" APPEND {N,S,E,W} TO THE GEOGRAPHIC COORDINATES """"
colors = ['darkmagenta','deeppink','darkturquoise','darkgreen','limegreen','orangered','coral']
LL = {}
LL[n] = ROI[n]
LL[yx] = ['({}°S, {}°W)'.format(round(abs(ROI[geo][0][0])), round(ROI[geo][0][1])),
          '({}°S, {}°W)'.format(round(abs(ROI[geo][1][0])), round(ROI[geo][1][1])),
          '({}°N, {}°W)'.format(round(ROI[geo][2][0]), round(ROI[geo][2][1])),
          '({}°N, {}°W)'.format(round(ROI[geo][3][0]), round(ROI[geo][3][1])),
          '({}°S, {}°W)'.format(round(abs(ROI[geo][4][0])), round(ROI[geo][4][1])),
          '({}°N, {}°W)'.format(round(ROI[geo][5][0]), round(ROI[geo][5][1])),
          '({}°N, {}°W)'.format(round(ROI[geo][6][0]), round(ROI[geo][6][1]))]

print('\nGeographic (lat-lon) coordinates of the selected ROIs are:')
for i in range(len(ROI['names'])):
    print(str(ROI['names'][i])+'   {}'.format(LL[yx][i]))

# Output:
# The point with geographic coordinates (-38,166.31) has pixel coordinates: y=85,x=54
# The point with geographic coordinates (-36.31,140.32) has pixel coordinates: y=90,x=68
# The point with geographic coordinates (15,134) has pixel coordinates: y=54,x=88
# The point with geographic coordinates (0.8,160.3) has pixel coordinates: y=58,x=69
# The point with geographic coordinates (-15.4,189.3) has pixel coordinates: y=64,x=44
# The point with geographic coordinates (30,160) has pixel coordinates: y=37,x=73
# The point with geographic coordinates (2.5,187.7) has pixel coordinates: y=50,x=49

# Geographic (lat-lon) coordinates of the selected ROIs are:
# Osiris crater   (38°S, 166°W)
# Menhit crater   (36°S, 140°W)
# Memphis Facula   (15°N, 134°W)
# Uruk Sulcus   (1°N, 160°W)
# Sippar Sulcus   (15°S, 189°W)
# Galileo Regio   (30°N, 160°W)
# Marius Regio   (2°N, 188°W)
```

```Python
fig, (ax1,ax2) = plt.subplots(1,2,figsize=(26,7))
fig.subplots_adjust(wspace = -0.05)
line_sample(ax1, 'G1GNGLOBAL01A (0.7-$\mu m$ image)\nReflectance  |  0°<i<60°, 0°<e<60°')
plot= ax1.imshow(g1g002_MASK_ie[0,:,:], alpha=1, cmap=cm.turbo)
cont_1 = ax1.contour(LATITUDES, 16, vmin=LAT_min, vmax=LAT_max, alpha=0.5, colors='black', linewidths=0.4)
cont_2 = ax1.contour(LONGITUDES, 24, vmin=LONG_min, vmax=LONG_max, alpha=0.5, colors='black', linewidths=0.4)
ax1.clabel(cont_1, inline=True, fontsize=8)
ax1.clabel(cont_2, inline=True, fontsize=8)
cbar = plt.colorbar(plot, ax=ax1, shrink=0.5)
cbar.ax.set_title('Refl.', fontsize=10)
max_refl = np.amax(g1g002_MASK_ie)
plot.set_clim(0, max_refl)

for i in range(len(ROI['yx'])):
    ax1.scatter(ROI['yx'][i][1], ROI['yx'][i][0], s=70, marker='x', color=colors[i], label=str(ROI['names'][i])+' {}'.format(ROI['yx'][i]))
    #sc.set_facecolor("none")
    ax1.legend()
EQUIRECT_PLOT(ax2, map_img_g1g002)
plt.show()
```
<img width="1004" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/2f7394ad-ff3e-41f4-b617-1880d70f123d">

> ROIs spectra (smoothed)
```Python
Osiris = sp.signal.savgol_filter(g1g002_akimov_adj[:,ROI['yx'][0][0],ROI['yx'][0][1]], 7, 2)    
Menhit = sp.signal.savgol_filter(g1g002_akimov_adj[:,ROI['yx'][1][0],ROI['yx'][1][1]], 7, 2)       
Memphis = sp.signal.savgol_filter(g1g002_akimov_adj[:,ROI['yx'][2][0],ROI['yx'][2][1]], 7, 2)      
Uruk = sp.signal.savgol_filter(g1g002_akimov_adj[:,ROI['yx'][3][0],ROI['yx'][3][1]], 7, 2)         
Sippar = sp.signal.savgol_filter(g1g002_akimov_adj[:,ROI['yx'][4][0],ROI['yx'][4][1]], 7, 2)      
Galileo = sp.signal.savgol_filter(g1g002_akimov_adj[:,ROI['yx'][5][0],ROI['yx'][5][1]], 7, 2)    
Marius = sp.signal.savgol_filter(g1g002_akimov_adj[:,ROI['yx'][6][0],ROI['yx'][6][1]], 7, 2)    
names = ['Osiris crater','Menhit crater','Memphis Facula','Uruk Sulcus','Sippar Sulcus','Galileo Regio','Marius Regio']
n_end = 7

# (SELECTED) ENDMEMBERS MATRIX
E_list = [Osiris, Menhit, Memphis, Uruk, Sippar, Galileo, Marius]
E = np.array(E_list)
```

```Python
fig, ax = plt.subplots(figsize=(15,8))
ax2 = ax.twiny()
new_tick_locations = np.array(sp_feat)
ax.set_xlim(0.7,5.2)
ax.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
ax.set_ylabel('Reflectance', fontsize=10)
ax.set_title('ROIs (single-pixel) reflectance spectra  |  0°<i<60°, 0°<e<60°', size=12)
MAX_spectrum = np.amax(g1g002_MASK_ie[:], axis=(1,2))
MIN_spectrum = np.amin(g1g002_MASK_ie[:],axis=(1,2))
ax.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', alpha=1, linewidth=1.6, label='Global Mean Spectrum')
plt.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.15)
for i in range(len(E)):
    ax.plot(wavel[:], E[i], alpha=.75, color=colors[i], linewidth=1, label=str(ROI['names'][i])+': {}'.format(LL['yx'][i]))    
for i in range(len(sp_feat)):
    plt.axvline(sp_feat[i], linestyle='--', linewidth=0.6, color='grey', alpha=0.7)
ax.grid(True, lw=0.4, ls='--', zorder=0, alpha=0.25)
ax2.set_xlim(ax.get_xlim())
ax2.set_xticks(new_tick_locations, labels=sp_feat, rotation=90)
ax.legend(frameon=False,loc='upper right')
plt.show()
```

<img width="752" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/567e0c9c-3d60-4652-9b12-6fe63e3712d9">

> Zoom in
```Python
fig, ax = plt.subplots(figsize=(16,10))
ax2 = ax.twiny()
new_tick_locations = np.array(sp_feat)
ax.set_xlim(0.7,5.2)
ax.set_ylim(-0.1,1.1)
ax.set_xticks([1,2,3,4,5], labels=[1,2,3,4,5])
ax.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
ax.set_ylabel('Reflectance (with offset)', fontsize=10)
ax.set_title('ROIs (single-pixel) reflectance spectra |  0°<i<60°, 0°<e<60°', size=12)
ax.plot(wavel[:], E[0],  color=colors[0], linewidth=1.7, label=str(ROI['names'][0])+': {}'.format(LL['yx'][0]))
ax.plot(wavel[:], E[1]+0.15,  color=colors[1], linewidth=1.7, label=str(ROI['names'][1])+': {}'.format(LL['yx'][1]))
ax.plot(wavel[:], E[2]+0.25,  color=colors[2], linewidth=1.7, label=str(ROI['names'][2])+': {}'.format(LL['yx'][2]))
ax.plot(wavel[:], E[3]+0.35,  color=colors[3], linewidth=1.7, label=str(ROI['names'][3])+': {}'.format(LL['yx'][3]))
ax.plot(wavel, avg_spectrum_filt_corr+0.45, color='snow', linewidth=1.7, label='Global Mean Spectrum')
ax.plot(wavel[:], E[4]+0.53,  color=colors[4], linewidth=1.7, label=str(ROI['names'][4])+': {}'.format(LL['yx'][4]))
ax.plot(wavel[:], E[5]+0.6, color=colors[5], linewidth=1.7, label=str(ROI['names'][5])+': {}'.format(LL['yx'][5]))
ax.plot(wavel[:], E[6]+0.67,  color=colors[6], linewidth=1.7, label=str(ROI['names'][6])+': {}'.format(LL['yx'][6])) 
for i in range(len(sp_feat)):
    plt.axvline(sp_feat[i], linestyle='--', linewidth=0.6, color='grey', alpha=0.7)
#ax.grid(True, lw=0.5, ls='--', zorder=0, alpha=0.2)
ax.legend(frameon=False,loc='upper right')
ax2.set_xlim(ax.get_xlim())
ax2.set_xticks(new_tick_locations, labels=sp_feat, rotation=90)
plt.show()
```

<img width="800" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/36d1f07a-6bd4-4d32-82ba-2b67d2666f75">

```Python
fig, (ax) = plt.subplots(figsize=(8,10))
ax.set_xlim(3, 5.2)
ax.set_ylim(-0.01,0.25)
ax.set_xticks([3,4,5], labels=[3,4,5])
ax.yaxis.tick_right()
ax2 = ax.twiny()
new_tick_locations = np.array([3.1, 3.6, 4.25])
ax.plot(wavel[:], E[0],  color=colors[0], linewidth=1.7, label=str(ROI['names'][0])+': {}'.format(LL['yx'][0]))
ax.plot(wavel[:], E[1]+0.015,  color=colors[1], linewidth=1.7, label=str(ROI['names'][1])+': {}'.format(LL['yx'][1]))
ax.plot(wavel[:], E[2]+0.022,  color=colors[2], linewidth=1.7, label=str(ROI['names'][2])+': {}'.format(LL['yx'][2]))
ax.plot(wavel[:], E[3]+0.038, color=colors[3], linewidth=1.7, label=str(ROI['names'][3])+': {}'.format(LL['yx'][3]))
ax.plot(wavel, avg_spectrum_filt_corr+0.06, color='snow', linewidth=1.7, label='Global Mean Spectrum')
ax.plot(wavel[:], E[4]+0.073,  color=colors[4], linewidth=1.7, label=str(ROI['names'][4])+': {}'.format(LL['yx'][4]))
ax.plot(wavel[:], E[5]+0.078,  color=colors[5], linewidth=1.7, label=str(ROI['names'][5])+': {}'.format(LL['yx'][5]))
ax.plot(wavel[:], E[6]+0.11,  color=colors[6], linewidth=1.7, label=str(ROI['names'][6])+': {}'.format(LL['yx'][6])) 
plt.axvline(3.1, linestyle='--', linewidth=0.6, color='grey', alpha=0.75)
plt.axvline(3.6, linestyle='--', linewidth=0.6, color='grey', alpha=0.75)
plt.axvline(4.25, linestyle='--', linewidth=0.6, color='grey', alpha=0.75)
ax.legend(frameon=False, loc='upper right')
ax2.set_xlim(ax.get_xlim())
ax2.set_xticks(new_tick_locations, labels=[3.1, 3.6, 4.25], rotation=90)
ax.grid(False)
plt.show()
```

<img width="417" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/12e3a222-e460-48e6-aa3f-c94905a8a7f6">

---

```Python
def EQUIRECT_PLOT_small(ax, data, i):
    """ Plot the data on equirectangular map """
    img_mappa = np.array(Image.open("Ganymede_Voyager_GalileoSSI.jpg"))
    ax.set_xlim(360,0)
    ax.set_ylim(-90,90)
    ax.yaxis.tick_right()
    major_ticksx = (360,270,180,90,0)
    minor_ticksx = np.arange(0,360,10)
    x_ticks_labels = ('360°W','270°W','180°W','90°W','0°W') 
    y_ticks_labels = ('-90°N','0°N','90°N') 
    major_ticksy = (-90,0,90)
    minor_ticksy = np.arange(-90,90,10)
    ax.set_xticks(major_ticksx)
    ax.set_xticks(minor_ticksx, minor=True)
    ax.set_xticklabels(x_ticks_labels)
    ax.set_yticks(major_ticksy)
    ax.set_yticks(minor_ticksy, minor=True)
    ax.set_yticklabels(y_ticks_labels)
    ax.imshow(img_mappa, extent=(360,0,-90,90))
    plot = ax.imshow(data[0,:,:], alpha=0.75, cmap=cm.turbo, extent=(0,360,90,-90)) 
    cbar = plt.colorbar(plot, ax=ax, location='bottom',fraction=0.036, pad=0.1, shrink=0.5)
    cbar.ax.set_title('Reflectance', fontsize=10)
    max_refl = np.amax(g1g002_MASK_ie)
    plot.set_clim(0, max_refl)
    ax.scatter(ROI['geographic coordinates'][i][1],ROI['geographic coordinates'][i][0], s=70, lw=2, c=colors[i], marker='x', label=str(ROI['names'][i])+' {}'.format(LL['yx'][i]))
```

```Python
fig, ((ax1,ax2),(ax3,ax4),(ax5,ax6),(ax7,ax8)) = plt.subplots(4,2, figsize=(24,30))
EQUIRECT_PLOT_small(ax1, map_img_g1g002, 0)
EQUIRECT_PLOT_small(ax2, map_img_g1g002, 1)
EQUIRECT_PLOT_small(ax3, map_img_g1g002, 2)
EQUIRECT_PLOT_small(ax4, map_img_g1g002, 3)
EQUIRECT_PLOT_small(ax5, map_img_g1g002, 4)
EQUIRECT_PLOT_small(ax6, map_img_g1g002, 5)
EQUIRECT_PLOT_small(ax7, map_img_g1g002, 6)
fig.delaxes(ax8)
plt.show()
```
