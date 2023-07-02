```Python
def line_sample(ax, name):
    """
    Set the specific visualization of NIMS images (120x100 grid)
       ax = name of the axes
       name = name of the plot
    """
    ax.set_xlim(0, 99)
    ax.set_ylim(119, 0)
    ax.set_xlabel('SAMPLE', fontsize=8, loc='left')
    ax.set_ylabel('LINE', fontsize=8, loc='top')
    ax.yaxis.set_label_coords(-0.02,0.07)
    ax.xaxis.set_label_coords(0.05,-0.03)
    major_ticksx = (0,25,50,75,100)
    minor_ticksx = np.arange(0, 100, 1)
    major_ticksy = (0,25,50,75,100)
    minor_ticksy = np.arange(0, 120, 1)
    ax.set_xticks(major_ticksx)
    ax.set_xticks(minor_ticksx, minor=True)
    ax.set_yticks(major_ticksy)
    ax.set_yticks(minor_ticksy, minor=True)
    ax.set_title(str(name))
```

```Python
def hsi_image(ax, name, data, k, cbar_title):
    """
    Show hyperspectral images in the specific visualization of NIMS images (120x100 grid)
    """
    ax.set_xlim(0, 99)
    ax.set_ylim(119, 0)
    ax.set_xlabel('SAMPLE', fontsize=8, loc='left')
    ax.set_ylabel('LINE', fontsize=8, loc='top')
    ax.yaxis.set_label_coords(-0.02,0.07)
    ax.xaxis.set_label_coords(0.05,-0.03)
    major_ticksx = (0,25,50,75,100)
    minor_ticksx = np.arange(0, 100, 1)
    major_ticksy = (0,25,50,75,100)
    minor_ticksy = np.arange(0, 120, 1)
    ax.set_xticks(major_ticksx)
    ax.set_xticks(minor_ticksx, minor=True)
    ax.set_yticks(major_ticksy)
    ax.set_yticks(minor_ticksy, minor=True)
    ax.set_title(str(name))
    plot = ax.imshow(data[k,:,:], cmap=cm.turbo)  
    cbar = plt.colorbar(plot, ax=ax, location='bottom',fraction=0.036, pad=0.1)
    cbar.ax.set_title(str(cbar_title), fontsize=10)
```

```Python
def hsi_tot_spectrum(ax, name, data):
    """
    Plot all the single-pixel I/F spectra of an hyperspectral image
    """
    ax.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
    ax.set_ylabel('Radiance Factor $I/F$', fontsize=10)
    ax.plot(wavel, np.reshape(data, [228, 12000]), color='gray', linewidth=0.1, alpha=0.25)
    ax.grid(True, lw=0.5, zorder=0, alpha=0.2)
    ax.set_title(str(name))
```

```Python
def geo_image(ax, name, geo_data, n_lines, v, V, mappa, cbar_title):
    """
    Show geometrical parameters associated with hyperspectral aquisition
    """
    ax.set_xlim(0, 99)
    ax.set_ylim(119, 0)
    ax.set_xlabel('SAMPLE', fontsize=8, loc='left')
    ax.set_ylabel('LINE', fontsize=8, loc='top')
    ax.yaxis.set_label_coords(-0.02,0.07)
    ax.xaxis.set_label_coords(0.05,-0.03)
    major_ticksx = (0,25,50,75,100)
    minor_ticksx = np.arange(0, 100, 1)
    major_ticksy = (0,25,50,75,100)
    minor_ticksy = np.arange(0, 120, 1)
    ax.set_xticks(major_ticksx)
    ax.set_xticks(minor_ticksx, minor=True)
    ax.set_yticks(major_ticksy)
    ax.set_yticks(minor_ticksy, minor=True)
    ax.set_title(str(name))
    plot = ax.imshow(geo_data, cmap=mappa)  #ex. of cmaps: viridis, Spectral
    cont = ax.contour(geo_data, n_lines, vmin=v, vmax=V, alpha=0.8, colors='black', linewidths=0.4)
    circle = plt.Circle((50,60), 46.5, alpha=0.4, color='black', linewidth=0.4, fill=False)
    ax.add_patch(circle)
    ax.clabel(cont, inline=True, fontsize=8)
    cbar = plt.colorbar(plot, ax=ax, location='bottom',fraction=0.036, pad=0.1)
    cbar.ax.set_title(str(cbar_title), fontsize=9)
```

```Python
def hsi_and_geo_image(ax, name, hsi_data, geo_data, n_lines, v, V, cbar_title, k):
    """
    Plot a hypespectral image with geometrical parameters superimposed
    """
    ax.set_xlim(0, 99)
    ax.set_ylim(119, 0)
    ax.set_xlabel('SAMPLE', fontsize=8, loc='left')
    ax.set_ylabel('LINE', fontsize=8, loc='top')
    ax.yaxis.set_label_coords(-0.02,0.07)
    ax.xaxis.set_label_coords(0.05,-0.03)
    major_ticksx = (0,25,50,75,100)
    minor_ticksx = np.arange(0, 100, 1)
    major_ticksy = (0,25,50,75,100)
    minor_ticksy = np.arange(0, 120, 1)
    ax.set_xticks(major_ticksx)
    ax.set_xticks(minor_ticksx, minor=True)
    ax.set_yticks(major_ticksy)
    ax.set_yticks(minor_ticksy, minor=True)
    ax.set_title(str(name))
    plot = ax.imshow(hsi_data[k,:,:], cmap=cm.turbo)
    cont = ax.contour(geo_data, n_lines, vmin=v, vmax=V, alpha=0.8, colors='black', linewidths=0.4)
    circle = plt.Circle((50,60), 46.5, alpha=0.4, color='black', linewidth=0.4, fill=False)
    ax.add_patch(circle)
    ax.clabel(cont, inline=True, fontsize=8)
    cbar = plt.colorbar(plot, ax=ax, shrink=0.5)
    cbar.ax.set_title(str(cbar_title), fontsize=10)
    max_refl = np.amax(hsi_data)
    plot.set_clim(0, max_refl)
```

```Python
def endmember_spectrum(ax, i,k, name, data):
    """
    Plot a I/F spectrum with main spectral features
    """
    #ax.set_xlim(0.7, 5.2)
    ax.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
    ax.set_ylabel('Reflectance', fontsize=10)
    ax.plot(wavel[i:k], data, color='black', linewidth=1, label='Measured spectrum')
    for i in range(len(sp_feat)):
        ax.axvline(sp_feat[i], linestyle='--', linewidth=0.5, color='black', alpha=0.3)
    ax.set_title(str(name))
```

```Python
def abundance_map(ax, compound, conc):
    """
    Show abundance map superimposed onto hypespectral image 
    """
    line_sample(ax, str(compound))
    plot = ax.imshow(conc.T, cmap=cm.Reds)    
    cont_1 = ax.contour(LATITUDES, 9, vmin=LAT_min, vmax=LAT_max, alpha=0.8, colors='black', linewidths=0.4)
    cont_2 = ax.contour(LONGITUDES, 10, vmin=LONG_min, vmax=LONG_max, alpha=0.8, colors='black', linewidths=0.4)
    ax.clabel(cont_1, inline=True, fontsize=8)
    ax.clabel(cont_2, inline=True, fontsize=8)
    for i in range(len(ROI['yx'])):
        ax.scatter(ROI['yx'][i][1],ROI['yx'][i][0], s=40, c=colors[i], marker='x', label=str(ROI['names'][i])+' {}'.format(ROI['yx'][i]))
    cbar = fig.colorbar(plot, ax=ax, shrink=0.5)
    circle = plt.Circle((50,60), 46.5, alpha=0.4, color='black', linewidth=0.4, fill=False)
    ax.add_patch(circle)
    #ax.legend()
```

```Python
def ab_map(ax, compound, conc):
    """
    Show abundance map 
    """
    line_sample(ax, str(compound))
    plot = ax.imshow(conc.T, cmap=cm.Reds)    
    cont_1 = ax.contour(LATITUDES, 9, vmin=LAT_min, vmax=LAT_max, alpha=0.8, colors='black', linewidths=0.4)
    cont_2 = ax.contour(LONGITUDES, 10, vmin=LONG_min, vmax=LONG_max, alpha=0.8, colors='black', linewidths=0.4)
    ax.clabel(cont_1, inline=True, fontsize=8)
    ax.clabel(cont_2, inline=True, fontsize=8)
    cbar = fig.colorbar(plot, ax=ax, shrink=0.5)
    circle = plt.Circle((50,60), 46.5, alpha=0.4, color='black', linewidth=0.4, fill=False)
    ax.add_patch(circle)
```

```Python
def ab_map_NOBAR(ax, compound, conc):
    """
    Show abundance map with no colorbar
    """
    line_sample(ax, str(compound))
    plot = ax.imshow(conc.T, cmap=cm.Reds)    
    cont_1 = ax.contour(LATITUDES, 9, vmin=LAT_min, vmax=LAT_max, alpha=0.8, colors='black', linewidths=0.4)
    cont_2 = ax.contour(LONGITUDES, 10, vmin=LONG_min, vmax=LONG_max, alpha=0.8, colors='black', linewidths=0.4)
    ax.clabel(cont_1, inline=True, fontsize=8)
    ax.clabel(cont_2, inline=True, fontsize=8)
    circle = plt.Circle((50,60), 46.5, alpha=0.4, color='black', linewidth=0.4, fill=False)
    ax.add_patch(circle)
```

```Python
def lincomb(coef, vect): 
    """
    Linear combination of vector, each one weighted with its own coefficient
    """
    Y = []
    for i in range(len(ROI['yx'])):   
        summa = 0
        for k in range(len(vect)):
            summa = summa + (coef[k, ROI['yx'][i][1], ROI['yx'][i][0]] * vect[k])
        Y.append(summa)
    return np.array(Y)
```

```Python
def find_nearest(array, value):
    """ 
    Given an array, find the element whose value is nearest to 'value' 
    """
    array = np.reshape(array, (12000))
    idx = (np.abs(array - value)).argmin()
    return array[idx]
```

```Python
def find_nearest_228(array, value):
    """ 
    Given an array, find the element whose value is nearest to 'value' 
    """
    idx = (np.abs(array - value)).argmin()
    return array[idx], idx
```

```Python
def find_YX_from_LatLon(lat_value, lon_value):
    """ 
    Find the pixel indexes (y,x) from geographic coordinates (lat,lon) 
    """
    a = abs( LATITUDES-lat_value ) + abs( LONGITUDES-lon_value )
    i,j = np.unravel_index(a.argmin(), a.shape)
    print("The point with geographic coordinates ({},{}) has pixel coordinates: y={},x={}".format(lat_value,lon_value,i,j))
    return i,j
```

```Python
def NormalizeData(data):
    return (data - np.nanmin(data)) / (np.nanmax(data) - np.nanmin(data))
```

---

Data projection on equirectangular map

```Python
def EQUIRECT_PROJ_and_PLOT(data):
    """ Project the data on equirectangular map and plot them """
    
    # """ Prepare the data for the projection """
    # """ LONGITUDES """
    LON = LONGITUDES.copy()
    LON[ np.where(~mask_ie == True) ] = np.nan

    # """ LATITUDES """
    LAT = LATITUDES.copy()
    LAT[ np.where(~mask_ie == True) ] = np.nan
    
    # """ Project the data on equirectangular map """
    map_img, long_arr, lat_arr = tools.mapping.longlat_to_map(data, LON, LAT, method='linear', ppd=1, visualise=False, 
                                                              obs_long=LON[60,50], print_progress=True)
    
    # """ Useful mask """
    for_mask = map_img.copy()
    for_mask[ for_mask > 0.] = 0.
    MASK = np.isnan(for_mask[0])

    # """ Plot """
    img_mappa = np.array(Image.open("Ganymede_Voyager_GalileoSSI.jpg"))
    fig, ax = plt.subplots(figsize=(16,10))
    ax.set_xlim(360,0)
    ax.set_ylim(-90,90)
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
    plot = ax.imshow(map_img[0,:,:], alpha=0.75, cmap=cm.turbo, extent=(0,360,90,-90)) 
    cbar = plt.colorbar(plot, ax=ax, shrink=0.5, pad=0.02)
    cbar.ax.set_title('Refl.', fontsize=10)
    max_refl = np.amax(data)
    plot.set_clim(0, max_refl)
    #ax.text(270, 85, 'T R A I L I N G    H E M I S P H E R E', c='white', alpha=0.75, fontsize=12, ha='center', va='center')
    #ax.text(90, 85, 'L E A D I N G    H E M I S P H E R E', c='white', alpha=0.75, fontsize=12, ha='center', va='center')
    plt.show()
    return
```

```Python
def EQUIRECT_PROJ(data):
    """ Project the data on equirectangular map """
    
    # """ Prepare the data for the projection """
    # """ LONGITUDES """
    LON = LONGITUDES.copy()
    LON[ np.where(~mask_ie == True) ] = np.nan

    # """ LATITUDES """
    LAT = LATITUDES.copy()
    LAT[ np.where(~mask_ie == True) ] = np.nan
    
    # """ Project the data on equirectangular map """
    map_img, long_arr, lat_arr = tools.mapping.longlat_to_map(data, LON, LAT, method='linear', ppd=1, visualise=False, 
                                                              obs_long=LON[60,50], print_progress=True)
    
    return map_img
```

```Python
def EQUIRECT_PROJ_raw(data):
    """ Project the (original, not masked) data on equirectangular map """
    
    # """ Prepare the data for the projection """
    mask_plot = (LONGITUDES>60)&(LONGITUDES<250)
    #mask_plot = (INCIDENCE_ANGLES>0)&(INCIDENCE_ANGLES<80)&(EMISSION_ANGLES>0)&(EMISSION_ANGLES<80)
    # """ LONGITUDES """
    LON = LONGITUDES.copy()
    #LON[ np.where(LON == np.NINF) ] = np.nan
    LON[ np.where(~mask_plot == True) ] = np.nan

    # """ LATITUDES """
    LAT = LATITUDES.copy()
    #LAT[ np.where(LAT == np.NINF) ] = np.nan
    LAT[ np.where(~mask_plot == True) ] = np.nan
    
    # """ Project the data on equirectangular map """
    map_img, long_arr, lat_arr = tools.mapping.longlat_to_map(data, LON, LAT, method='linear', ppd=1, visualise=False, 
                                                              obs_long=LON[60,50], print_progress=True)
    
    return map_img
```

```Python
def Ganymede_plot(ax):
    img_mappa = np.array(Image.open("Ganymede_Voyager_GalileoSSI.jpg"))
    ax.set_xlim(360,0)
    ax.set_ylim(-90,90)
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
    ax.text(270, 85, 'T R A I L I N G    H E M I S P H E R E', c='white', alpha=0.75, fontsize=12, ha='center', va='center')
    ax.text(90, 85, 'L E A D I N G    H E M I S P H E R E', c='white', alpha=0.75, fontsize=12, ha='center', va='center')
    ax.grid(alpha=0.1, which='minor')
    ax.grid(alpha=0.3, which='major')
    return
```

```Python
def EQUIRECT_PLOT(ax, data):
    """ Plot the data on equirectangular map """
    img_mappa = np.array(Image.open("Ganymede_Voyager_GalileoSSI.jpg"))
    ax.set_xlim(360,0)
    ax.set_ylim(-90,90)
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
    #ax.text(270, 85, 'T R A I L I N G    H E M I S P H E R E', c='white', alpha=1, fontsize=12, ha='center', va='center')
    #ax.text(90, 85, 'L E A D I N G    H E M I S P H E R E', c='white', alpha=1, fontsize=12, ha='center', va='center')
    ax.imshow(img_mappa, extent=(360,0,-90,90))
    plot = ax.imshow(data[0,:,:], alpha=0.75, cmap=cm.turbo, extent=(0,360,90,-90)) 
    #ax.imshow(~MASK, cmap=cm.gray, extent=(0,360,90,-90), alpha=0.1)
    cbar = plt.colorbar(plot, ax=ax, location='bottom',fraction=0.036, pad=0.1, shrink=0.5)
    cbar.ax.set_title('Reflectance', fontsize=10)
    max_refl = np.amax(g1g002_MASK_ie)
    plot.set_clim(0, max_refl)
    answer = input('Do you want to plot the ROIs?')
    if answer == 'yes':
        for i in range(len(LL['yx'])):
            ax.scatter(ROI['geographic coordinates'][i][1],ROI['geographic coordinates'][i][0], s=70, lw=2, c=colors[i], marker='x', label=str(ROI['names'][i])+' {}'.format(LL['yx'][i]))
        ax.legend(frameon=False)
        plt.show()
    else: 
        plt.show()
    return
```

```Python
def abund_EQUIRECT_PROJ_and_PLOT(ax, data, cmap, alpha):
    """ Project the data on equirectangular map and plot them """
    
    # """ Prepare the data for the projection """
    # """ LONGITUDES """
    LON = LONGITUDES.copy()
    LON[ np.where(~mask_ie == True) ] = np.nan

    # """ LATITUDES """
    LAT = LATITUDES.copy()
    LAT[ np.where(~mask_ie == True) ] = np.nan
    
    # """ Project the data on equirectangular map """
    map_img, long_arr, lat_arr = tools.mapping.longlat_to_map(data, LON, LAT, method='linear', ppd=1, visualise=False, 
                                                              obs_long=LON[60,50], print_progress=True)
    
    # """ Plot """
    img_mappa = np.array(Image.open("Ganymede_Voyager_GalileoSSI.jpg"))
    ax.set_xlim(360,0)
    ax.set_ylim(-90,90)
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
    #ax.text(270, 85, 'T R A I L I N G    H E M I S P H E R E', c='white', alpha=0.75, fontsize=12, ha='center', va='center')
    #ax.text(90, 85, 'L E A D I N G    H E M I S P H E R E', c='white', alpha=0.75, fontsize=12, ha='center', va='center')
    ax.imshow(img_mappa, extent=(360,0,-90,90))
    plot = ax.imshow(map_img, alpha=alpha, cmap=cmap, extent=(0,360,90,-90))
    #ax.imshow(~MASK, cmap=cm.gray, extent=(0,360,90,-90), alpha=0.1)
    cbar = plt.colorbar(plot, ax=ax, shrink=0.5, pad=0.02)
    cbar.ax.set_title('Ab.(%)', fontsize=10)
    max_refl = np.amax(data)
    plot.set_clim(0, max_refl)

    return
```
