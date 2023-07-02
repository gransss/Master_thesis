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

---

Useful functions for spectral unmixing

```Python
### """ FUNCTIONS FOR THE ROI SPECTRA """ ###
def SPECTRAL_UNMIXING(i,j):
    """ SHOW AVAILABLE END MEMBERS """
    for l in range(len(tot['names'])-3):
        print('Index: '+str(l)+',  Compound: '+str(tot['names'][l])+', ['+str(np.where(wavel==np.min(SpLib_wavel[l]))[0][0])+','+str(np.where(wavel==np.max(SpLib_wavel[l]))[0][0])+']')
    for l in range(12,17):
        print('Index: '+str(l)+', Compound: '+str(tot['names'][l])+', ['+str(np.where(wavel==np.min(SpLib_wavel[l]))[0][0])+','+str(np.where(wavel==np.max(SpLib_wavel[l]))[0][0])+']')
    """ SHOW SPECTRAL FEATURES (WAVELENGTH AND INDEX) """
    print("\nSpectral features WAVELENGTHS: ", sp_feat)
    print("Spectral features INDEXES: ", idxs)
  
    """ SHOW THE SPECTRA IN THE SELECTED RANGE """
    fig, ax1 = plt.subplots(figsize=(12,10))
    ax2 = ax1.twiny()
    new_tick_locations = np.array(sp_feat)
    ax1.set_xlim(0.9012,5.0782)
    ax1.set_ylim(-0.15,4.9)
    ax1.set_yticks([])
    ax1.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
    ax1.set_title('Spectral library  |  Reflectance (with offset)', size=12)
    ax1.plot(extended_wavel[15], extended_spectrum[15]-0.05, color='black', label='{}'.format(tot['names'][15]))
    ax1.plot(extended_wavel[16], extended_spectrum[16]-0.99,color='lightgrey', label='{}'.format(tot['names'][16]))
    ax1.plot(extended_wavel[11], extended_spectrum[11]-0.09,color='goldenrod', label='{}'.format(tot['names'][11]))
    ax1.plot(extended_wavel[13], extended_spectrum[13]+0.26,color='orange', label='{}'.format(tot['names'][13]))
    ax1.plot(extended_wavel[1], extended_spectrum[1]+0.55,color='darkorange', label='{}'.format(tot['names'][1]))
    ax1.plot(extended_wavel[3], extended_spectrum[3]+1.15,color='orangered', label='{}'.format(tot['names'][3]))
    ax1.plot(extended_wavel[10], extended_spectrum[10]+1.2,color='indigo', label='{}'.format(tot['names'][10]))
    ax1.plot(extended_wavel[9], extended_spectrum[9]+1.45,color='rebeccapurple', label='{}'.format(tot['names'][9]))
    ax1.plot(extended_wavel[8], extended_spectrum[8]+1.55,color='mediumpurple', label='{}'.format(tot['names'][8]))
    ax1.plot(extended_wavel[2], extended_spectrum[2]+2.35, color='forestgreen', label='{}'.format(tot['names'][2]))
    ax1.plot(extended_wavel[0], extended_spectrum[0]+2.15, color='yellowgreen', label='{}'.format(tot['names'][0]))
    ax1.plot(extended_wavel[7], extended_spectrum[7]+2.6,color='brown', label='{}'.format(tot['names'][7]))
    ax1.plot(extended_wavel[14], extended_spectrum[14]+3.16,color='indianred', label='{}'.format(tot['names'][14]))
    ax1.plot(extended_wavel[12], extended_spectrum[12]+3.55,color='lightcoral', label='{}'.format(tot['names'][12]))
    ax1.plot(extended_wavel[6], extended_spectrum[6]+3.68, color='royalblue', label='{}'.format(tot['names'][6]))
    ax1.plot(extended_wavel[5], extended_spectrum[5]+3.75, color='cornflowerblue', label='{}'.format(tot['names'][5]))
    ax1.plot(extended_wavel[4], extended_spectrum[4]+3.8, color='skyblue', label='{}'.format(tot['names'][4]))
    PA = wavel[i]
    MA = wavel[j]
    ax1.axvspan(PA,MA, facecolor='grey', alpha=0.15)
    for f in range(len(sp_feat)):
        ax1.axvline(sp_feat[f], linestyle='--', linewidth=0.5, color='black', alpha=0.6)
    ax2.set_xlim(ax1.get_xlim())
    ax2.set_xticks(new_tick_locations, labels=sp_feat, rotation=90)
    ax1.text(0.88, -0.05, "Black", c='black', va='center', ha='right',)
    ax1.text(0.88, 0.03, "White", c='grey', va='center', ha='right')
    ax1.text(0.88, 0.64, "$Na_2Mg(SO_4)_2\cdot4H_2O$\n(bloedite)", c='goldenrod', va='center',ha='right')
    ax1.text(0.88, 0.84, "$Na_2SO_4\cdot10H_2O$\n(mirabilite)", c='orange', va='center',ha='right')
    ax1.text(0.88, 1.3, "(1) $MgSO_4\cdot6H_2O$\n(hexahydrite)", c='darkorange', va='center',ha='right')
    ax1.text(0.88, 1.55, "(3) $MgCl_2\cdot6H_2O$\n(bischofite)", c='orangered', va='center',ha='right')
    ax1.text(0.88, 2.02, "(10) $Na_2CO_3\cdot10H_2O$\n(natron)", c='indigo', va='center',ha='right')
    ax1.text(0.88, 2.27, "(9) $Na_2CO_3\cdot1H_2O$\n(thermonatrite)", c='rebeccapurple', va='center',ha='right')
    ax1.text(0.88, 2.5, "(8) $Na_2CO_3$\n(natrite)", c='mediumpurple', va='center',ha='right')
    ax1.text(3.97, 2.3, "(0) $H_2O_2$\n(hydrogen peroxide)", c='yellowgreen', va='center')
    ax1.text(0.88, 3.1, "(2) $H_2SO_4\cdot8H_2O$\n(hydrated sulfuric acid)", c='forestgreen', va='center',ha='right')
    ax1.text(5.1, 2.66, "(7) $NH_4Cl$\n(ammonium chloride)", c='brown', va='center')
    ax1.text(5.1, 3.18, "(14) $NaCl\cdot2H_2O$\n(hydrohalite)", c='indianred', va='center')
    ax1.text(5.1, 3.58, "(12) $NaHCO_3$\n(sodium bicarbonate)", c='lightcoral', va='center')
    ax1.text(0.88, 4.27, "(6) $H_2O$ ice 200$\mu$m", c='royalblue', va='center',ha='right')
    ax1.text(0.88, 4.6, "(5) $H_2O$ ice 20$\mu$m", c='cornflowerblue', va='center',ha='right')
    ax1.text(0.88, 4.8, "(4) $CO_2$ ice", c='skyblue', va='center',ha='right')
    plt.show()
    
    """ ENDMEMBERS """
    my_arr = []
    a = int(input("How many end members?"))
    for k in range(a):
        my_arr.append( EM[int(input("Which end member?"))][i:j] )
    ENDM = np.array(my_arr, dtype=object)
    print('ENDMEMBERS ARRAY: ', ENDM.shape)
    
    """ DATA MATRIX """
    MATRIX = np.reshape(g1g002_MASK_ie.T[:,:,i:j], [12000,(j-i)])   # prima c'era g1g002_filtered_arr, e non 'MASK_ie'
    
    """ FIND ABUNDANCES WITH FCLS METHOD """
    ABUNDANCES = pysptools.abundance_maps.amaps.FCLS(MATRIX, ENDM)
    
    """ RE-RAVEL ABUNDANCE MAPS """
    n = len(ENDM)
    ABUND = []
    for k in range(n):
        map_lin = np.zeros((12000))
        map_lin[valid_array] = ABUNDANCES[:,k]
        reshaped = np.reshape(map_lin,(100,120))
        reshaped_masked2 = np.ma.masked_array(reshaped, ~mask_ie.T)  #~
        ABUND.append(reshaped_masked2)
    ABUND = np.ma.asarray(ABUND) # or asarray
    print('ABUNDANCES ARRAY: ', ABUND.shape)

    """ COMPUTE THE MODELED SPECTRA """
    MODEL = lincomb(ABUND, ENDM)
    #MODEL = NormalizeData(MODEL)
    print('MODELED SPECTRA ARRAY: ',MODEL.shape)
    
    return ENDM, ABUND, MODEL
```

```Python
def SP_UNMIX_maps(endmembers, abundances):
    """ ENDMEMBERS """
    print(' """Create the abundance maps of the selected endmembers""" ')
    my_arrr = []
    a = len(endmembers)
    for t in range(a):
        my_arrr.append( int(input("End member index:")) )
    INDEXES = np.array(my_arrr)
    
    """ PLOT """
    if a <= 5 :
        nrow = 1
        ncol = len(endmembers)
        fig, axs = plt.subplots(nrow, ncol, figsize=(5*ncol,5), sharex=True, sharey=True)
        fig.subplots_adjust(wspace = 0.1)
        for i, ax in enumerate(fig.axes):
            ax.set_ylabel(str(i))
        for k in range(len(INDEXES)):
            abundance_map(axs[k], '{}'.format(tot['names'][INDEXES[k]]), abundances[k])
        plt.show()
    else:
        print('NO')
    return
```

```Python
def ABUND_TABLE(endmembers, abundances):
    """ PUT THE ABUNDANCE COEFF. IN A PROPER ARRAY """
    print(' """Visualize the abundance coefficients of the selected ROIs""" ')
    a = len(endmembers)
    datafe = []
    for i in range(len(ROI['yx'])):   
        for k in range(len(endmembers)):
            dat = [ abundances[k, ROI['yx'][i][1], ROI['yx'][i][0]]  ]
            datafe.append(dat)
    dataf = np.reshape(datafe,(7,len(endmembers)))
    
    """  ENDMEMBERS INDEX"""
    my_arr = []
    a = len(endmembers)
    for t in range(a):
        my_arr.append( int(input("End member index:")) )
    INDEXES = np.array(my_arr)
    
    """ CREATE COLUMN NAMES """
    col = []
    new_SPLIB_names = ['$$H_2O_2$$ (hydrogen peroxide)','$$MgSO_4\cdot6H_2O$$ (hexahydrite)', '$$H_2SO_4\cdot8H_2O$$ (hydrated sulfuric acid)','$$MgCl_2\cdot6H_2O$$ (bischofite)','$$CO_2$$ ice','$$H_2O$$ ice (20 $$\mu$$m)','$$H_2O$$ ice (200 $$\mu$$m)','$$NH_4Cl$$ (ammonium chloride)','$$Na_2CO_3$$ (natrite)','$$Na_2CO_3\cdot1H_2O$$ (thermonatrite)','$$Na_2CO_3\cdot10H_2O$$ (natron)','$$Na_2Mg(SO_4)_2\cdot4H_2O$$ (bloedite)','$$NaHCO_3$$ (sodium bicarbonate)', '$$Na_2SO_4\cdot10H_2O$$ (mirabilite)','$$NaCl\cdot2H_2O$$ (hydrohalite)','Black (synth.)','White (synth.)']
    for k in range(a):
        col.append( str(new_SPLIB_names[INDEXES[k]]) )
        
    """ CREATE pd.DataFrame """
    classes = names
    columns = col
    return pd.DataFrame(dataf, classes, columns)
```

```Python
def endmember_spectrum2(ax, name, data):
    #ax.set_xlim(0.7, 5.2)
    ax.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
    ax.set_ylabel('Reflectance', fontsize=10)
    ax.plot(wavel[:], data, color='snow', linewidth=1.5, label='Measured spectrum')
    #ax.fill_between(wavel[i:k], MIN_spectrum, MAX_spectrum, color='gray', alpha=0.1)
    for i in range(len(sp_feat)):
        ax.axvline(sp_feat[i], linestyle='--', linewidth=0.7, color='grey', alpha=0.5)
    ax.set_title(str(name))
    #ax.legend(title='Model abundances:')
```

```Python
def CHI2(i,j, model):
    """ Compute the chi2 """
    Os = sp.signal.savgol_filter(g1g002_akimov_adj[i:j,ROI['yx'][0][0],ROI['yx'][0][1]], 7, 2)       
    Menh = sp.signal.savgol_filter(g1g002_akimov_adj[i:j,ROI['yx'][1][0],ROI['yx'][1][1]], 7, 2)       
    Memph = sp.signal.savgol_filter(g1g002_akimov_adj[i:j,ROI['yx'][2][0],ROI['yx'][2][1]], 7, 2)      
    Uru = sp.signal.savgol_filter(g1g002_akimov_adj[i:j,ROI['yx'][3][0],ROI['yx'][3][1]], 7, 2)         
    Sipp = sp.signal.savgol_filter(g1g002_akimov_adj[i:j,ROI['yx'][4][0],ROI['yx'][4][1]], 7, 2)      
    Gali = sp.signal.savgol_filter(g1g002_akimov_adj[i:j,ROI['yx'][5][0],ROI['yx'][5][1]], 7, 2)    
    Mar = sp.signal.savgol_filter(g1g002_akimov_adj[i:j,ROI['yx'][6][0],ROI['yx'][6][1]], 7, 2)    
    E_slice = [Os,Menh,Memph,Uru,Sipp,Gali,Mar]
    obs = np.array(E_slice, dtype=object)

    # MODELED DATA
    MODEL_chi = np.array(model, dtype=float)
    exp = ma.masked_equal(MODEL_chi, 0.)
    
    # COMPUTE THE CHI2
    CHI2 = []
    for s in range(len(MODEL_chi)):
        chisq, p = stats.chisquare(f_obs=obs[s], f_exp=exp[s])
        CHI2.append(chisq)
    CHI2 = np.array(CHI2)
    return CHI2
```

```Python
def SP_UNMIX_fit(i,j, endmembers, model, original_EM, chi2):
    print(' """Visualize the modeled and measured spectra in the selected ROIs""" ')
    fig, ((ax1,ax2,ax3,ax4),(ax5,ax6,ax7,ax8)) = plt.subplots(2,4,figsize=(23,12), sharex=True, sharey=True)
    endmember_spectrum2(ax1, 'ROI: '+str(names[0])+' {}'.format(ROI['yx'][0]), original_EM[0][:])
    endmember_spectrum2(ax2, 'ROI: '+str(names[1])+' {}'.format(ROI['yx'][1]), original_EM[1][:])
    endmember_spectrum2(ax3, 'ROI: '+str(names[2])+' {}'.format(ROI['yx'][2]), original_EM[2][:])
    endmember_spectrum2(ax4, 'ROI: '+str(names[3])+' {}'.format(ROI['yx'][3]), original_EM[3][:])
    endmember_spectrum2(ax5, 'ROI: '+str(names[4])+' {}'.format(ROI['yx'][4]), original_EM[4][:])
    endmember_spectrum2(ax6, 'ROI: '+str(names[5])+' {}'.format(ROI['yx'][5]), original_EM[5][:])
    endmember_spectrum2(ax7, 'ROI: '+str(names[6])+' {}'.format(ROI['yx'][6]), original_EM[6][:])
    ax1.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', linewidth=0.2)
    ax2.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', linewidth=0.2)
    ax3.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', linewidth=0.2)
    ax4.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', linewidth=0.2)
    ax5.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', linewidth=0.2)
    ax6.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', linewidth=0.2)
    ax7.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', linewidth=0.2)
    ax1.plot(wavel[i:j], model[0], c='red', lw=1, label='Modeled spectrum')
    ax2.plot(wavel[i:j], model[1], c='red', lw=1, label='Modeled spectrum')
    ax3.plot(wavel[i:j], model[2], c='red', lw=1, label='Modeled spectrum')
    ax4.plot(wavel[i:j], model[3], c='red', lw=1, label='Modeled spectrum') 
    ax5.plot(wavel[i:j], model[4], c='red', lw=1, label='Modeled spectrum') 
    ax6.plot(wavel[i:j], model[5], c='red', lw=1, label='Modeled spectrum')
    ax7.plot(wavel[i:j], model[6], c='red', lw=1, label='Modeled spectrum')
    fig.delaxes(ax8)
    MAX_spectrum = np.amax(g1g002_MASK_ie[:], axis=(1,2))
    MIN_spectrum = np.amin(g1g002_MASK_ie[:],axis=(1,2))
    ax1.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.2)
    ax2.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.2)
    ax3.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.2)
    ax4.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.2)
    ax5.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.2)
    ax6.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.2)
    ax7.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.2)
    ax1.legend(title='$\chi^2$: {}'.format(round(chi2[0],4)))
    ax2.legend(title='$\chi^2$: {}'.format(round(chi2[1],4)))
    ax3.legend(title='$\chi^2$: {}'.format(round(chi2[2],4)))
    ax4.legend(title='$\chi^2$: {}'.format(round(chi2[3],4)))
    ax5.legend(title='$\chi^2$: {}'.format(round(chi2[4],4)))
    ax6.legend(title='$\chi^2$: {}'.format(round(chi2[5],4)))
    ax7.legend(title='$\chi^2$: {}'.format(round(chi2[6],4)))
    plt.show()
    return
```

```Python
def SP_UNMIX_fit_VERT(i,j, endmembers, model, original_EM, chi2):
    print(' """Visualize the modeled and measured spectra in the selected ROIs""" ')
    fig, (ax1,ax2,ax3,ax4,ax5,ax6,ax7) = plt.subplots(7,1,figsize=(13,29), sharex=True, sharey=True)  
    fig.subplots_adjust(hspace=0.0)
    ax1.set_xlim(0.7,5.2)
    ax2.set_xlim(0.7,5.2)
    ax3.set_xlim(0.7,5.2)
    ax4.set_xlim(0.7,5.2)
    ax5.set_xlim(0.7,5.2)
    ax6.set_xlim(0.7,5.2)
    ax7.set_xlim(0.7,5.2)
    ax8 = ax1.twiny()
    new_tick_locations = np.array(sp_feat)
    endmember_spectrum2(ax1, '', original_EM[0][:])
    endmember_spectrum2(ax2, '', original_EM[1][:])
    endmember_spectrum2(ax3, '', original_EM[2][:])
    endmember_spectrum2(ax4, '', original_EM[3][:])
    endmember_spectrum2(ax5, '', original_EM[4][:])
    endmember_spectrum2(ax6, '', original_EM[5][:])
    endmember_spectrum2(ax7, '', original_EM[6][:])
    ax1.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', linewidth=0.2)
    ax2.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', linewidth=0.2)
    ax3.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', linewidth=0.2)
    ax4.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', linewidth=0.2)
    ax5.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', linewidth=0.2)
    ax6.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', linewidth=0.2)
    ax7.plot(wavel[:], avg_spectrum_filt_corr[:], color='snow', linewidth=0.2)
    ax1.plot(wavel[i:j], model[0], c='red', lw=1, label='Modeled spectrum')
    ax2.plot(wavel[i:j], model[1], c='red', lw=1, label='Modeled spectrum')
    ax3.plot(wavel[i:j], model[2], c='red', lw=1, label='Modeled spectrum')
    ax4.plot(wavel[i:j], model[3], c='red', lw=1, label='Modeled spectrum') 
    ax5.plot(wavel[i:j], model[4], c='red', lw=1, label='Modeled spectrum') 
    ax6.plot(wavel[i:j], model[5], c='red', lw=1, label='Modeled spectrum')
    ax7.plot(wavel[i:j], model[6], c='red', lw=1, label='Modeled spectrum')
    MAX_spectrum = np.amax(g1g002_MASK_ie[:], axis=(1,2))
    MIN_spectrum = np.amin(g1g002_MASK_ie[:],axis=(1,2))
    ax1.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.2)
    ax2.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.2)
    ax3.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.2)
    ax4.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.2)
    ax5.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.2)
    ax6.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.2)
    ax7.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.2)
    ax1.legend(frameon=False, title='ROI: '+str(names[0])+' '+str(ROI['yx'][0])+'\n$\chi^2$: {}'.format(round(chi2[0],4)))   
    ax2.legend(frameon=False, title='ROI: '+str(names[1])+' '+str(ROI['yx'][1])+'\n$\chi^2$: {}'.format(round(chi2[1],4))) 
    ax3.legend(frameon=False, title='ROI: '+str(names[2])+' '+str(ROI['yx'][2])+'\n$\chi^2$: {}'.format(round(chi2[2],4))) 
    ax4.legend(frameon=False, title='ROI: '+str(names[3])+' '+str(ROI['yx'][3])+'\n$\chi^2$: {}'.format(round(chi2[3],4))) 
    ax5.legend(frameon=False, title='ROI: '+str(names[4])+' '+str(ROI['yx'][4])+'\n$\chi^2$: {}'.format(round(chi2[4],4))) 
    ax6.legend(frameon=False, title='ROI: '+str(names[5])+' '+str(ROI['yx'][5])+'\n$\chi^2$: {}'.format(round(chi2[5],4))) 
    ax7.legend(frameon=False, title='ROI: '+str(names[6])+' '+str(ROI['yx'][6])+'\n$\chi^2$: {}'.format(round(chi2[6],4)))
    ax8.set_xlim(ax1.get_xlim())
    ax8.set_xticks(new_tick_locations)
    ax8.set_xticklabels(sp_feat, rotation=90)
    answer = input('Do you want to save the image?')
    if answer == 'yes':
        plt.show()
        title = input('Name of the saved image:')
        fig.savefig(str(title))
    else:
        plt.show()
    return
```

```Python
def abund_EQUIRECT_PROJ_and_PLOT_family(ax, data, cmap, alpha):
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
    max_ab = np.amax((dark.T)*100)
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
    plot = ax.imshow(map_img, alpha=alpha, cmap=cmap, extent=(0,360,90,-90))
    cbar = plt.colorbar(plot, ax=ax, pad=0.02)
    cbar.ax.set_title('Ab.(%)', fontsize=10)
    plot.set_clim(0, max_ab)

    return
```
