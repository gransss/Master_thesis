### Compositional maps

Best-fit spectral unmixing: **thirteen end-members**
> Spectrally-flat synthetic material, H2O ice (20 and 200 μm), hydrated sulfuric acid, hexahydrite, bischofite, ammonium chloride, bloedite, mirabilite, hydrohalite, natrite, thermonatrite

```Python
En10d, Cn10d, YMODELn10d = SPECTRAL_UNMIXING(16,110)       
CHI2_n10d = CHI2(16,110, YMODELn10d)
#SP_UNMIX_fit_VERT(16,110, En10d, YMODELn10d, E, CHI2_n10d)     

# 15,16,5,6,2,1,3,7,11,13,14,8,9

# Output
# Index: 0,  Compound: $H_2O_2$ (hydrogen peroxide), [124,173]
# Index: 1,  Compound: $MgSO_4\cdot6H_2O$ (hexahydrite), [0,171]
# Index: 2,  Compound: $H_2SO_4\cdot8H_2O$ (hydrated sulfuric acid), [25,110]
# Index: 3,  Compound: $MgCl_2\cdot6H_2O$ (bischofite), [0,204]
# Index: 4,  Compound: $CO_2$ ice, [16,219]
# Index: 5,  Compound: $H_2O$ ice (small grains, 20$\mu$m), [16,221]
# Index: 6,  Compound: $H_2O$ ice (large grains, 200$\mu$m), [16,221]
# Index: 7,  Compound: $NH_4Cl$ (ammonium chloride), [0,227]
# Index: 8,  Compound: $Na_2CO_3$ (natrite), [8,182]
# Index: 9,  Compound: $Na_2CO_3\cdot1H_2O$ (thermonatrite), [0,171]
# Index: 10,  Compound: $Na_2CO_3\cdot10H_2O$ (natron), [0,171]
# Index: 11,  Compound: $Na_2Mg(SO_4)_2\cdot4H_2O$ (bloedite), [0,110]
# Index: 12,  Compound: $NaHCO_3$ (sodium bicarbonate), [35,227]
# Index: 13,  Compound: $Na_2SO_4\cdot10H_2O$ (mirabilite), [0,215]
# Index: 12, Compound: $NaHCO_3$ (sodium bicarbonate), [35,227]
# Index: 13, Compound: $Na_2SO_4\cdot10H_2O$ (mirabilite), [0,215]
# Index: 14, Compound: $NaCl\cdot2H_2O$ (hydrohalite), [42,227]
# Index: 15, Compound: Black (synth.), [0,227]
# Index: 16, Compound: White (synth.), [0,227]

# Spectral features WAVELENGTHS:  [1.04, 1.25, 1.5, 1.57, 1.65, 2.02, 3.1, 3.6, 4.25]
# Spectral features INDEXES:  [ 25  34  44  50  56  87 137 159 186]
```

<img width="773" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/ffabd985-2c39-42b3-a38b-155a57609462">

```Python
ENDMEMBERS ARRAY:  (13, 94)
ABUNDANCES ARRAY:  (13, 100, 120)
MODELED SPECTRA ARRAY:  (7, 94)
```

```Python
def endmember_spectrum2(ax, name, data):
    #ax.set_xlim(0.7, 5.2)
    ax.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
    ax.set_ylabel('Reflectance', fontsize=10)
    ax.plot(wavel[:], data, color='snow', linewidth=1.5, label='Observation')
    #ax.fill_between(wavel[i:k], MIN_spectrum, MAX_spectrum, color='gray', alpha=0.1)
    for i in range(len(sp_feat)):
        ax.axvline(sp_feat[i], linestyle='--', linewidth=0.7, color='grey', alpha=0.5)
    ax.set_title(str(name))
    #ax.legend(title='Model abundances:')
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
    #max_ab = np.amax((dark.T)*100)
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
    #ax.imshow(~MASK, cmap=cm.gray, extent=(0,360,90,-90), alpha=0.1)
    #cbar = plt.colorbar(plot, ax=ax, shrink=0.5, pad=0.02)   #fraction=0.036
    cbar = plt.colorbar(plot, ax=ax, location='top', fraction=0.023, pad=0.05, aspect=35)   #SHORT 23, LONG 53
    cbar.ax.set_title('Abundance (%)', fontsize=10)
    #ax.grid(alpha=0.1, which='minor')
    #ax.grid(alpha=0.3, which='major')
    #plot.set_clim(0, max_ab)

    return
```

```Python
fig, ((ax1,ax2),(ax3,ax4),(ax5,ax6),(ax7,ax8),(ax9,ax10),(ax11,ax12)) = plt.subplots(6,2, figsize=(16,33))   #30,32
#fig.subplots_adjust(wspace = -0.4)
abund_EQUIRECT_PROJ_and_PLOT(ax1, (Cn10d[0].T+Cn10d[1].T)*100., cmap=cm.turbo, alpha=0.8)
abund_EQUIRECT_PROJ_and_PLOT(ax2, (Cn10d[2].T)*100., cmap=cm.turbo, alpha=0.8)
abund_EQUIRECT_PROJ_and_PLOT(ax3, (Cn10d[3].T)*100., cmap=cm.turbo, alpha=0.8)
abund_EQUIRECT_PROJ_and_PLOT(ax4, (Cn10d[4].T)*100., cmap=cm.turbo, alpha=0.8)
abund_EQUIRECT_PROJ_and_PLOT(ax5, (Cn10d[5].T)*100., cmap=cm.turbo, alpha=0.8)
abund_EQUIRECT_PROJ_and_PLOT(ax6, (Cn10d[6].T)*100., cmap=cm.turbo, alpha=0.8)
abund_EQUIRECT_PROJ_and_PLOT(ax7, (Cn10d[7].T)*100., cmap=cm.turbo, alpha=0.8)
abund_EQUIRECT_PROJ_and_PLOT(ax8, (Cn10d[8].T)*100., cmap=cm.turbo, alpha=0.8)
abund_EQUIRECT_PROJ_and_PLOT(ax9, (Cn10d[9].T)*100., cmap=cm.turbo, alpha=0.8)
abund_EQUIRECT_PROJ_and_PLOT(ax10, (Cn10d[10].T)*100., cmap=cm.turbo, alpha=0.8)
abund_EQUIRECT_PROJ_and_PLOT(ax11, (Cn10d[11].T)*100., cmap=cm.turbo, alpha=0.8)
abund_EQUIRECT_PROJ_and_PLOT(ax12, (Cn10d[12].T)*100., cmap=cm.turbo, alpha=0.8)
ax1.set_title('Spectrally-flat grey material (black + white spectra)')
ax2.set_title(str(tot['names'][5]))
ax3.set_title(str(tot['names'][6]))
ax4.set_title(str(tot['names'][2]))
ax5.set_title(str(tot['names'][1]))    # 15,16,5,6,2,1,3,7,11,13,14,8,9 
ax6.set_title(str(tot['names'][3]))
ax7.set_title(str(tot['names'][7]))
ax8.set_title(str(tot['names'][11]))
ax9.set_title(str(tot['names'][13]))
ax10.set_title(str(tot['names'][14]))
ax11.set_title(str(tot['names'][8]))
ax12.set_title(str(tot['names'][9]))
plt.show()
```

```Python
fig, ((ax1,ax2),(ax3,ax4)) = plt.subplots(2,2, figsize=(18,10))
#fig.subplots_adjust(wspace=0., hspace = -0.4)
plot1 = abund_EQUIRECT_PROJ_and_PLOT(ax1, (Cn10d[0].T+Cn10d[1].T)*100, cmap=cm.turbo, alpha=0.8)
plot2 = abund_EQUIRECT_PROJ_and_PLOT(ax2, (Cn10d[2].T+Cn10d[3].T)*100, cmap=cm.turbo, alpha=0.8)
plot3 = abund_EQUIRECT_PROJ_and_PLOT(ax3, (Cn10d[5].T+Cn10d[6].T+Cn10d[7].T+Cn10d[8].T+Cn10d[9].T+Cn10d[10].T+Cn10d[11].T+Cn10d[12].T)*100, cmap=cm.turbo, alpha=0.8)
plot4 = abund_EQUIRECT_PROJ_and_PLOT(ax4, (Cn10d[4].T)*100, cmap=cm.turbo, alpha=0.8)

#ax1.set_title('Spectrally-flat grey material (black + white spectra)')
#ax2.set_title('$H_2O$ ice (20 and 200 $\mu$m grain size)')
#ax3.set_title('All salts')
#ax4.set_title('Acid: $H_2SO_4\cdot8H_2O$')
plt.show()
```
<img width="901" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/cd7aa826-0d33-43eb-bf6b-a3e9ba6efd5d">

```Python
dark = Cn10d[0].T+Cn10d[1].T
water = Cn10d[2]+Cn10d[3]
sal = Cn10d[5].T+Cn10d[6].T+Cn10d[7].T+Cn10d[8].T+Cn10d[9].T+Cn10d[10].T+Cn10d[11].T+Cn10d[12].T
aci = Cn10d[4]

print(np.mean(dark)*100, np.max(dark)*100)
print(np.mean(water)*100, np.max(water)*100)
print(np.mean(sal)*100, np.max(sal)*100)
print(np.mean(aci)*100, np.max(aci)*100)

# Output
# 61.22540579920715 86.86442412436008
# 18.655282588769413 44.523061059004476
# 20.081326850892815 59.668244743630794
# 0.03798474170386503 1.9263019785284996
```

```Python
print('{}  |  MEAN: {}, MAX: {}'.format(str(tot['names'][1]),np.mean(Cn10d[5])*100,np.max(Cn10d[5])*100) )  
print('{}  |  MEAN: {}, MAX: {}'.format(str(tot['names'][3]),np.mean(Cn10d[6])*100,np.max(Cn10d[6])*100) )  
print('{}  |  MEAN: {}, MAX: {}'.format(str(tot['names'][7]),np.mean(Cn10d[7])*100,np.max(Cn10d[7])*100) )  
print('{}  |  MEAN: {}, MAX: {}'.format(str(tot['names'][11]),np.mean(Cn10d[8])*100,np.max(Cn10d[8])*100) )  
print('{}  |  MEAN: {}, MAX: {}'.format(str(tot['names'][13]),np.mean(Cn10d[9])*100,np.max(Cn10d[9])*100) )  
print('{}  |  MEAN: {}, MAX: {}'.format(str(tot['names'][14]),np.mean(Cn10d[10])*100,np.max(Cn10d[10])*100) )  
print('{}  |  MEAN: {}, MAX: {}'.format(str(tot['names'][8]),np.mean(Cn10d[11])*100,np.max(Cn10d[11])*100) ) 
print('{}  |  MEAN: {}, MAX: {}'.format(str(tot['names'][9]),np.mean(Cn10d[12])*100,np.max(Cn10d[12])*100) )

# Output 
# $MgSO_4\cdot6H_2O$ (hexahydrite)  |  MEAN: 5.079363464911717, MAX: 14.530828595161438
# $MgCl_2\cdot6H_2O$ (bischofite)  |  MEAN: 0.002280966022256846, MAX: 1.1373111046850681
# $NH_4Cl$ (ammonium chloride)  |  MEAN: 0.004934179675013704, MAX: 0.9276258759200573
# $Na_2Mg(SO_4)_2\cdot4H_2O$ (bloedite)  |  MEAN: 6.595728279525426, MAX: 22.330713272094727
# $Na_2SO_4\cdot10H_2O$ (mirabilite)  |  MEAN: 1.9982947535281574, MAX: 18.759511411190033
# $NaCl\cdot2H_2O$ (hydrohalite)  |  MEAN: 6.393267380041892, MAX: 19.06621605157852
# $Na_2CO_3$ (natrite)  |  MEAN: 0.005111239778267214, MAX: 1.654895581305027
# $Na_2CO_3\cdot1H_2O$ (thermonatrite)  |  MEAN: 0.0023465874100874035, MAX: 1.8379677087068558
```

```Python
ABUND_TABLE(En10d, Cn10d*100)   # 15,16,5,6,2,1,3,7,11,13,14,8,9

# Output
# """Visualize the abundance coefficients of the selected ROIs""" 
```

<img width="1154" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/c6f44849-af81-46d4-b9da-c7476a5e27c0">

```Python
abund_in_ROIs10d = []
for i in range(len(ROI['yx'])):   
    for k in range(len(En10d)):
        dat2 = [ Cn10d[k, ROI['yx'][i][1], ROI['yx'][i][0]]  ]
        abund_in_ROIs10d.append(dat2)
    #abund_in_ROIs10d.append([ Cn10d[0, ROI['yx'][i][1], ROI['yx'][i][0]]  ]+[ Cn10d[1, ROI['yx'][i][1], ROI['yx'][i][0]]  ])
abund_in_ROIs10d = np.reshape(abund_in_ROIs10d,(7,len(En10d)))
abund_in_ROIs10d.shape

# Output
# (7,13)
```

```Python
synth = []
water_ice = []
acid = []
salts = []
for i in range(len(abund_in_ROIs10d)):
    synth.append(abund_in_ROIs10d[i][0]+abund_in_ROIs10d[i][1])
    water_ice.append(abund_in_ROIs10d[i][2]+abund_in_ROIs10d[i][3])
    acid.append(abund_in_ROIs10d[i][4])
    salts.append(abund_in_ROIs10d[i][5]+abund_in_ROIs10d[i][6]+abund_in_ROIs10d[i][7]+abund_in_ROIs10d[i][8]+abund_in_ROIs10d[i][9]+abund_in_ROIs10d[i][10]+abund_in_ROIs10d[i][11]+abund_in_ROIs10d[i][12])
synth = np.array([synth]).T
water_ice = np.array([water_ice]).T
acid = np.array([acid]).T
salts = np.array([salts]).T
```

```Python
print('Synthetic: ',synth*100, '\n\nWater ice: ', water_ice*100, '\n\nAcid: ',acid*100, '\n\nSalts: ',salts*100)

# Output
# Synthetic:  [[ 9.27667021]
#  [35.17758586]
#  [56.84432536]
#  [56.94426522]
#  [67.03874394]
#  [76.5488416 ]
#  [73.41210246]] 

# Water ice:  [[35.96540527]
#  [27.66535672]
#  [23.40707742]
#  [25.27996844]
#  [17.11072568]
#  [ 9.18235895]
#  [14.90382579]] 

# Acid:  [[1.28418396e-02]
#  [3.06800735e-04]
#  [1.64491632e-04]
#  [1.02106310e-04]
#  [2.84406468e-05]
#  [1.00842990e-03]
#  [1.26564672e-05]] 

# Salts:  [[54.74508417]
#  [37.15675401]
#  [19.74843208]
#  [17.77566621]
#  [15.85049994]
#  [14.2677934 ]
#  [11.68405868]]
```

```Python
abund_in_ROIs10d_NEW = np.hstack((abund_in_ROIs10d, synth, water_ice, acid, salts))
abund_in_ROIs10d_NEW.shape

# Output
# (7,17)
```

```Python
colors3 = ['darkmagenta','deeppink','darkturquoise','darkgreen','limegreen','orangered','coral']
def abund_BAR_plot2(i, vector):        # 15,16,5,6,2,1,3,7,11,13,14,8,9 
    ax1.set_xlim(0.9,2.4)
    ax8 = ax1.twiny()
    new_tick_locations = np.array([1.04, 1.25, 1.5, 1.57, 1.65, 2.02])
    endmember_spectrum2(ax1, '', E[i][:])
    ax1.plot(wavel[16:110], YMODELn10d[i], c='red', lw=1.7, label='Model')
    MAX_spectrum = sp.signal.savgol_filter(np.amax(g1g002_MASK_ie[:], axis=(1,2)), 5, 2)  
    MIN_spectrum = sp.signal.savgol_filter(np.amin(g1g002_MASK_ie[:],axis=(1,2)), 5, 2)   
    ax1.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.15)
    ax1.legend(frameon=False)   
    ax1.text(2.1, 0.6, '$\chi^2$: {}'.format(round(CHI2_n10d[i],4)), fontsize='large')#, fontweight='bold')
    ax8.set_xlim(ax1.get_xlim())
    ax8.set_xticks(new_tick_locations)
    ax8.set_xticklabels([1.04, 1.25, 1.5, 1.57, 1.65, 2.02], rotation=90)
    
    ax2.set_ylim(0, 100)
    ax2.set_xlim(-0.5, 16.5)
    #ax2.set_ylabel('Abundance (%)', fontsize=12)
    ax2.set_title('Abundances | ROI: %s'%(names[i]), size=12)
    lab = ['Black','White','$H_2O$ ice (20 $\mu$m)','$H_2O$ ice (200 $\mu$m)','$H_2SO_4\cdot8H_2O$','$MgSO_4\cdot6H_2O$ (hexahydr.)','$MgCl_2\cdot6H_2O$ (bischof.)','$NH_4Cl$ (ammonium chl.)','$Na_2Mg(SO_4)_2\cdot4H_2O$ (bloed.)','$Na_2SO_4\cdot10H_2O$ (mirab.)','$NaCl\cdot2H_2O$ (hydrohalite)','$Na_2CO_3$ (natrite)','$Na_2CO_3\cdot1H_2O$ (thermonatr.)','All synthetic','All ices','Acid','Salts']
    colors = ['lightgrey','lightgrey','cornflowerblue','cornflowerblue','mediumseagreen','lightcoral','lightcoral','lightcoral','lightcoral','lightcoral','lightcoral','lightcoral','lightcoral','lightgrey','cornflowerblue','mediumseagreen','lightcoral']
    ticksx = np.arange(0,17,1)
    ax2.set_xticks(ticksx,[])#, labels=lab, rotation=90, fontsize='large')
    ax2.bar(ticksx, (vector[i])*100, width=0.5, color=colors)
    #ax1.grid(True, lw=0.5, zorder=0, alpha=0.2)
    ax2.axvline(12.5, c='grey',ls='--',lw=1)
    #for k in range(0,17):
     #   ax2.text(k,98,lab[k],va='top',ha='center',rotation=90,fontsize='xx-large')
    return

def abund_BAR_plot_alone(ax,i, vector):        # 15,16,5,6,2,1,3,7,11,13,14,8,9 
    ax.set_ylim(0, 100)
    ax.set_xlim(-0.5, 16.5)
    ax.set_ylabel('Abundance (%)', fontsize=12)
    ax.set_title('Abundances | ROI: %s'%(names[i]), size=12)
    lab = ['Black','White','$H_2O$ ice (20 $\mu$m)','$H_2O$ ice (200 $\mu$m)','$H_2SO_4\cdot8H_2O$','$MgSO_4\cdot6H_2O$ (hexahydr.)','$MgCl_2\cdot6H_2O$ (bischof.)','$NH_4Cl$ (ammonium chl.)','$Na_2Mg(SO_4)_2\cdot4H_2O$ (bloed.)','$Na_2SO_4\cdot10H_2O$ (mirab.)','$NaCl\cdot2H_2O$ (hydrohalite)','$Na_2CO_3$ (natrite)','$Na_2CO_3\cdot1H_2O$ (thermonatr.)','All synthetic','All ices','Acid','Salts']
    colors = ['lightgrey','lightgrey','cornflowerblue','cornflowerblue','mediumseagreen','lightcoral','lightcoral','lightcoral','lightcoral','lightcoral','lightcoral','lightcoral','lightcoral','lightgrey','cornflowerblue','mediumseagreen','lightcoral']
    ticksx = np.arange(0,17,1)
    ax.set_xticks(ticksx,[])#, labels=lab, rotation=90)
    ax.bar(ticksx, (vector[i])*100, width=0.5, color=colors)
    #ax1.grid(True, lw=0.5, zorder=0, alpha=0.2)
    ax.axvline(12.5, c='grey',ls='--',lw=1)
    #for k in range(0,17):
     #   ax.text(k,98,lab[k],va='top',ha='center',rotation=90)
    return
```

```Python
fig, (ax1,ax2) = plt.subplots(1,2,figsize=(22,7) )     
fig.subplots_adjust(wspace=0.05) 
ax1.set_xlim(0.9,2.4)
ax8 = ax1.twiny()
new_tick_locations = np.array([1.04, 1.25, 1.5, 1.57, 1.65, 2.02])
endmember_spectrum2(ax1, '', E[0][:])
ax1.plot(wavel[16:110], YMODELn10d[0], c='red', lw=1.7, label='Model')
MAX_spectrum = sp.signal.savgol_filter(np.amax(g1g002_MASK_ie[:], axis=(1,2)), 5, 2)  
MIN_spectrum = sp.signal.savgol_filter(np.amin(g1g002_MASK_ie[:],axis=(1,2)), 5, 2)   
ax1.fill_between(wavel[:], MIN_spectrum, MAX_spectrum, color='snow', alpha=0.15)
ax1.legend(frameon=False)   
ax1.text(2.11, 0.66, '$\chi^2$: {}'.format(round(CHI2_n10d[0],4)), fontsize='large')#, fontweight='bold')
ax8.set_xlim(ax1.get_xlim())
ax8.set_xticks(new_tick_locations)
ax8.set_xticklabels([1.04, 1.25, 1.5, 1.57, 1.65, 2.02], rotation=90)

ax2.yaxis.tick_right()
#abund_BAR_plot_alone(ax2, 0, abund_in_ROIs10d_NEW)
ax2.set_ylim(0, 100)
#ax2.set_xlim(-0.5, 16.5)
#ax2.set_ylabel('Abundance (%)', fontsize=12)
#ax2.set_title('Abundances | ROI: %s'%(names[0]), size=12)
lab = ['Black','White','$H_2O$ ice (20 $\mu$m)','$H_2O$ ice (200 $\mu$m)','$H_2SO_4\cdot8H_2O$','$MgSO_4\cdot6H_2O$ (hexahydr.)','$MgCl_2\cdot6H_2O$ (bischof.)','$NH_4Cl$ (ammonium chl.)','$Na_2Mg(SO_4)_2\cdot4H_2O$ (bloed.)','$Na_2SO_4\cdot10H_2O$ (mirab.)','$NaCl\cdot2H_2O$ (hydrohalite)','$Na_2CO_3$ (natrite)','$Na_2CO_3\cdot1H_2O$ (thermonatr.)','All synthetic','All ices','Acid','Salts']
colors = ['lightgrey','lightgrey','cornflowerblue','cornflowerblue','mediumseagreen','lightcoral','lightcoral','lightcoral','lightcoral','lightcoral','lightcoral','lightcoral','lightcoral','lightgrey','cornflowerblue','mediumseagreen','lightcoral']
edges = ['darkgrey','darkgrey','royalblue','royalblue','seagreen','indianred','indianred','indianred','indianred','indianred','indianred','indianred','indianred','darkgrey','royalblue','seagreen','indianred']
ticksx = np.arange(0,17,1)
ax2.set_xticks(ticksx,[])#, labels=lab, rotation=90)
ax2.bar(ticksx, (abund_in_ROIs10d_NEW[0])*100, width=0.5, color=colors, edgecolor=edges)
#ax1.grid(True, lw=0.5, zorder=0, alpha=0.2)
ax2.axvline(12.5, c='grey',ls='--',lw=1)
#for k in range(0,17):
 #   ax.text(k,98,lab[k],va='top',ha='center',rotation=90)
plt.show()
fig.savefig('UNMIX_Osiris2.svg', transparent=True, format='svg', dpi=600)
```

<img width="1104" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/8c4f7719-2c77-44c7-ae96-d7c93a656cc0">

[...and so on with the rest of the ROIs...]
