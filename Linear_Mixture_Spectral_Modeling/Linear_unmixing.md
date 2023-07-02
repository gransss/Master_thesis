### Linear Spectral Unmixing

> Visualize the names of the uploaded files (.txt format)
```Python
ls\Spectral_library_new

# Output
# 20141122_hexahydrite_Tminus150__convolution_NIM.txt
# Carlson_1999_H2O2_convolution_NIM.txt
# Carlson_1999_H2SO4_8H2O_140K_convolution_NIM.txt
# Dalton_2012_75-106um_120K_convolution_NIMS.txt
# MgCl2_6H2O_250um_125K_convolution_NIM.txt
# Soderblom_2009_CO2 ice_100micro_convolution_NIM.txt
# Soderblom_2009_H2Oice_200micron_convolution_NIM.txt
# Soderblom_2009_H2Oice_20micron_convolution_NIM.txt
# Thomas_2019_hydrohalite_convolution_NIMS.txt
# VNIR-refl_Natron_75-100_128K_convolution_NIMS.txt
# ammonium_chloride_PI_Cloutis_45_convolution_NIM.txt
# mirabilite_20-50um_125K_convolution_NIM.txt
# natrite_75-100_128K_convolution_NIM.txt
# splib07a_Bloedite_GDS147_NIC4aa_convolution_NIM.txt
# splib07a_Sodium_Bicarbonate_GDS_convolution_NIM.txt
# thermonatrite_75-100_128K_convolution_NIMS.txt
```

> Define a function which extracts the wavelength and the convoluted spectrum arrays from each data file
```Python
def extract_spectrum_2_columns(dataname):
    """Extract the wavelength and convoluted spectrum arrays from the data file"""
    
    # Choose the name of the two arrays
    arrays = {}
    d = 'dataname'
    w = 'wavelength'
    c = 'convolution'
    arrays[d] = str(dataname)
    arrays[w] = []
    arrays[c] = []
    
    filepath = 'Spectral_library_new/'+str(dataname)
    
    # Read the file
    with open(filepath, 'r', encoding='ISO-8859-1') as f:
        FileContent = f.readlines()[2:]
        FileContent = [ x.replace('\t',',') for x in FileContent ]
        FileContent = [ x.replace('\n',',') for x in FileContent ]
        #FileContent = [ x.replace('    ', ',') for x in FileContent ]
    
    # Create a DataFrame
    df = pd.DataFrame([sub.split(",") for sub in FileContent])
    df_selection = df.iloc[:,0:3]
    
    # SELECT COLUMNS INTO NUMPY ARRAYS
    # Prima colonna (wavelength)
    df1 = df_selection.iloc[:,0]              # pandas.core.series.Series
    wave = np.array(df1)                      # ndarray, dtype('O') = string
    waveleng = [float(i) for i in wave]       # ndarray, dtype = float64   
    waveleng = np.asarray(waveleng, dtype=np.float32)/1000.
    arrays[w] = np.round(waveleng,4)
    #print("\nYour array 'wavelength' with shape {} is:\n{}".format(wavelength.shape, wavelength))

    # Seconda colonna (convolution)
    df2 = df_selection.iloc[:,1]              # pandas.core.series.Series
    convol = np.array(df2)                    # ndarray, dtype('O') = string
    convolution = [float(i) for i in convol]  # ndarray, dtype = float64  
    convolution = np.asarray(convolution)
    arrays[c] = convolution
    #print("\nYour array 'convolution' with shape {} is:\n{}".format(convolution.shape, convolution))
    
    return arrays[d], arrays[w], arrays[c]
```

```Python
name_h2o2, wavel_h2o2, conv_h2o2 = extract_spectrum_2_columns('Carlson_1999_H2O2_convolution_NIM.txt')  
name_hexah, wavel_hexah, conv_hexah = extract_spectrum_2_columns('20141122_hexahydrite_Tminus150__convolution_NIM.txt')  
name_h2so4, wavel_h2so4, conv_h2so4 = extract_spectrum_2_columns('Carlson_1999_H2SO4_8H2O_140K_convolution_NIM.txt')  
name_MgCl2, wavel_MgCl2, conv_MgCl2 = extract_spectrum_2_columns('MgCl2_6H2O_250um_125K_convolution_NIM.txt')  
name_CO2, wavel_CO2, conv_CO2 = extract_spectrum_2_columns('Soderblom_2009_CO2 ice_100micro_convolution_NIM.txt')  
name_H2O, wavel_H2O, conv_H2O = extract_spectrum_2_columns('Soderblom_2009_H2Oice_20micron_convolution_NIM.txt')  
name_H2O_200, wavel_H2O_200, conv_H2O_200 = extract_spectrum_2_columns('Soderblom_2009_H2Oice_200micron_convolution_NIM.txt')  
name_ammonium, wavel_ammonium, conv_ammonium = extract_spectrum_2_columns('ammonium_chloride_PI_Cloutis_45_convolution_NIM.txt')  
name_natrite, wavel_natrite, conv_natrite = extract_spectrum_2_columns('natrite_75-100_128K_convolution_NIM.txt') 
name_thermonatrite, wavel_thermonatrite, conv_thermonatrite = extract_spectrum_2_columns('thermonatrite_75-100_128K_convolution_NIMS.txt') 
name_natron, wavel_natron, conv_natron = extract_spectrum_2_columns('VNIR-refl_Natron_75-100_128K_convolution_NIMS.txt') 
name_bloedite, wavel_bloedite, conv_bloedite = extract_spectrum_2_columns('Dalton_2012_75-106um_120K_convolution_NIMS.txt')  
name_sodiumBi, wavel_sodiumBi, conv_sodiumBi = extract_spectrum_2_columns('splib07a_Sodium_Bicarbonate_GDS_convolution_NIM.txt') 
name_mirabilite, wavel_mirabilite, conv_mirabilite = extract_spectrum_2_columns('mirabilite_20-50um_125K_convolution_NIM.txt')  
name_hydrohalite, wavel_hydrohalite, conv_hydrohalite = extract_spectrum_2_columns('Thomas_2019_hydrohalite_convolution_NIMS.txt') 

# CREATE THE ZERO AND ONE FLAT SPECTRA
null_refl = np.full((228), 0.)
one_refl = np.full((228), 1.)
wavel_null = wavel
wavel_one = wavel
```

```Python
tot = {}
n = 'names'
l = 'wavelength'
s = 'spectra'
tot[n] = ['$H_2O_2$ (hydrogen peroxide)','$MgSO_4\cdot6H_2O$ (hexahydrite)', '$H_2SO_4\cdot8H_2O$ (hydrated sulfuric acid)','$MgCl_2\cdot6H_2O$ (bischofite)','$CO_2$ ice','$H_2O$ ice (small grains, 20$\mu$m)','$H_2O$ ice (large grains, 200$\mu$m)','$NH_4Cl$ (ammonium chloride)','$Na_2CO_3$ (natrite)','$Na_2CO_3\cdot1H_2O$ (thermonatrite)','$Na_2CO_3\cdot10H_2O$ (natron)','$Na_2Mg(SO_4)_2\cdot4H_2O$ (bloedite)','$NaHCO_3$ (sodium bicarbonate)','$Na_2SO_4\cdot10H_2O$ (mirabilite)','$NaCl\cdot2H_2O$ (hydrohalite)','Black (synth.)','White (synth.)']
tot[l] = [wavel_h2o2, wavel_hexah, wavel_h2so4, wavel_MgCl2, wavel_CO2, wavel_H2O, wavel_H2O_200, wavel_ammonium, wavel_natrite, wavel_thermonatrite, wavel_natron, wavel_bloedite, wavel_sodiumBi, wavel_mirabilite, wavel_hydrohalite, wavel_null, wavel_one]
tot[s] = [conv_h2o2, conv_hexah, conv_h2so4, conv_MgCl2, conv_CO2, conv_H2O, conv_H2O_200, conv_ammonium, conv_natrite, conv_thermonatrite, conv_natron, conv_bloedite, conv_sodiumBi, conv_mirabilite, conv_hydrohalite, null_refl, one_refl]

# THE WAVELENGTHS ARRAY
SpLib_wavel = np.array(tot[l], dtype=object)
print(SpLib_wavel.shape)    # Output: (17,)

# THE SPECTRAL LIBRARY ARRAY
SpLib_conv = np.array(tot[s], dtype=object) 
print(SpLib_conv.shape)     # Output: (17,)
```

> Investigate the number of values in each array
```Python
for k in range(len(tot[l])):
    print('{}:   {}'.format(tot['names'][k],tot[l][k].shape))

# Output
# $H_2O_2$ (hydrogen peroxide):   (50,)
# $MgSO_4\cdot6H_2O$ (hexahydrite):   (172,)
# $H_2SO_4\cdot8H_2O$ (hydrated sulfuric acid):   (86,)
# $MgCl_2\cdot6H_2O$ (bischofite):   (205,)
# $CO_2$ ice:   (204,)
# $H_2O$ ice (small grains, 20$\mu$m):   (206,)
# $H_2O$ ice (large grains, 200$\mu$m):   (206,)
# $NH_4Cl$ (ammonium chloride):   (228,)
# $Na_2CO_3$ (natrite):   (175,)
# $Na_2CO_3\cdot1H_2O$ (thermonatrite):   (172,)
# $Na_2CO_3\cdot10H_2O$ (natron):   (172,)
# $Na_2Mg(SO_4)_2\cdot4H_2O$ (bloedite):   (111,)
# $NaHCO_3$ (sodium bicarbonate):   (193,)
# $Na_2SO_4\cdot10H_2O$ (mirabilite):   (216,)
# $NaCl\cdot2H_2O$ (hydrohalite):   (186,)
# Black (synth.):   (228,)
# White (synth.):   (228,)
```

> Define some useful functions:
```Python
def plot_spectrum_topL(ax,i):
    #ax.plot(tot['wavelength'][i], tot['spectra'][i], label='{}'.format(tot['names'][i]))
    ax.plot(SpLib_wavel[i], SpLib_conv[i], label='{}'.format(tot['names'][i]))
    ax.set_ylim(-0.2,1.2)
    major_ticksy = (0,0.2,0.4,0.6,0.8,1)
    ax.set_yticks(major_ticksy)
    #ax.set_yticklabels([]) 
    ax.xaxis.set_major_formatter(plt.NullFormatter())
    ax.grid(lw=0.3, zorder=0, alpha=0.30)
    for k in range(len(sp_feat)):
        ax.axvline(sp_feat[k], linestyle='--', linewidth=0.6, color='black', alpha=0.7)
    ax.legend()

def plot_spectrum_bottomL(ax,i):
    #ax.plot(tot['wavelength'][i], tot['spectra'][i], label='{}'.format(tot['names'][i]))
    ax.plot(SpLib_wavel[i], SpLib_conv[i], label='{}'.format(tot['names'][i]))
    ax.set_ylim(-0.05,1.05)
    ax.set_ylim(-0.2,1.2)
    major_ticksy = (0,0.2,0.4,0.6,0.8,1)
    major_ticksx = (1,2,3,4,5)
    ax.set_yticks(major_ticksy)
    ax.set_xticks(major_ticksx)
    #ax.set_yticklabels([]) 
    ax.spines['top'].set_visible(False)
    #plt.setp(ax.get_xticklabels(), visible=True)
    #ax.tick_params(labelbottom=False)
    ax.grid(lw=0.3, zorder=0, alpha=0.30)
    for k in range(len(sp_feat)):
        ax.axvline(sp_feat[k], linestyle='--', linewidth=0.6, color='black', alpha=0.7)
    ax.legend()
    
def plot_spectrum_specialL(ax,i):
    #ax.plot(tot['wavelength'][i], tot['spectra'][i], label='{}'.format(tot['names'][i]))
    ax.plot(SpLib_wavel[i], SpLib_conv[i], label='{}'.format(tot['names'][i]))
    ax.set_ylim(-0.05,1.05)
    ax.set_ylim(-0.2,1.2)
    major_ticksy = (0,0.2,0.4,0.6,0.8,1)
    ax.set_yticks(major_ticksy)
    #ax.set_yticklabels([]) 
    ax.spines['bottom'].set_visible(False)
    ax.spines['top'].set_visible(False)
    plt.setp(ax.get_xticklabels(), visible=False) 
    #ax.get_xaxis().set_ticks([])
    #ax.xaxis.set_major_formatter(plt.NullFormatter())
    ax.grid(lw=0.3, zorder=0, alpha=0.30)
    for k in range(len(sp_feat)):
        ax.axvline(sp_feat[k], linestyle='--', linewidth=0.6, color='black', alpha=0.7)
    ax.legend()
```

> Extend the range of lab's spectra so that it's comparable for everyone (228 values)
```Python
extended_wavel = []
extended_spectrum = []
for k in range(len(SpLib_wavel)):  
    i = SpLib_wavel[k][0]     # first element
    j = SpLib_wavel[k][-1]    # last element
    first_value, first_idx = find_nearest_228(wavel, i)
    last_value, last_idx = find_nearest_228(wavel, j)
    PRE = np.full(first_idx, np.nan)
    POST = np.full(228-(last_idx+1), np.nan)
    conc = np.concatenate((PRE, SpLib_wavel[k], POST), axis=None)
    conc2 = np.concatenate((PRE, SpLib_conv[k], POST), axis=None)
    extended_wavel.append(conc)
    extended_spectrum.append(conc2)
extended_wavel = np.array(extended_wavel, dtype=np.float32)
extended_spectrum = np.array(extended_spectrum, dtype=np.float32)
```
>Control:
```Python
fig,ax = plt.subplots()
for k in range(len(extended_wavel)):
    ax.plot(extended_wavel[k], c='red', ls='--')
ax.plot(wavel, c='blue', alpha=0.3, label='NIMS $\lambda$ range')
ax.grid(True, lw=0.5, zorder=0, alpha=0.60)
plt.legend()
plt.show()
```
<img width="306" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/d69f8318-89cf-4249-b92a-1d725d48c409">

---

### Spectral Unmixing

Estimate abundances: **FCLS method (Fully Constrained Least Squares)**

`help(pysptools.abundance_maps.amaps.FCLS)`

```Python
extended_spectrum_MASK = np.ma.masked_invalid(extended_spectrum)
EM = np.ma.filled(extended_spectrum_MASK, fill_value=0.)
print('The shape of the spectral library was: ',extended_spectrum.shape)     # Output: (17, 228)
print('The shape of the spectral library now is: ',EM.shape)                 # Output: (17, 228)
```

```Python
print(find_nearest_228(wavel,1.4), find_nearest_228(wavel,2.25))
print(find_nearest_228(wavel,2.4), find_nearest_228(wavel,3))
print(find_nearest_228(wavel,2.8), find_nearest_228(wavel,3.7))
print(find_nearest_228(wavel,4.0), find_nearest_228(wavel,4.5))
```

```Python
fig, ax1 = plt.subplots(figsize=(18,28))   #18,15
ax2 = ax1.twiny()
new_tick_locations = np.array(sp_feat)
ax1.set_xlim(0.9012,5.0782)    #0.7101,5.2365
ax1.set_ylim(-0.15,4.9)
ax1.set_yticks([])
ax1.set_xticks([1,2,3,4,5])
ax1.set_xlabel('Wavelength ($\mu m$)', fontsize=10)
#ax1.set_ylabel('Reflectance (with offset)', fontsize=10, labelpad=10)
ax1.set_title('Spectral library  |  Reflectance (with offset)', size=12)
#SYNTHETIC
ax1.plot(extended_wavel[15], extended_spectrum[15]-0.05, lw=1.9, color='dimgrey', label='{}'.format(tot['names'][15]))
ax1.plot(extended_wavel[16], extended_spectrum[16]-0.99, lw=1.9, color='snow', label='{}'.format(tot['names'][16]))
#CARBONATES
ax1.plot(extended_wavel[12], extended_spectrum[12]+0.08, lw=2, color='darkmagenta', label='{}'.format(tot['names'][12]))
ax1.plot(extended_wavel[10], extended_spectrum[10]+0.13, lw=1.9, color='mediumvioletred', label='{}'.format(tot['names'][10]))
ax1.plot(extended_wavel[9], extended_spectrum[9]+0.36, lw=1.9, color='deeppink', label='{}'.format(tot['names'][9]))
ax1.plot(extended_wavel[8], extended_spectrum[8]+0.44, lw=1.9, color='hotpink', label='{}'.format(tot['names'][8]))
#SULFATES
ax1.plot(extended_wavel[13], extended_spectrum[13]+1.14, lw=1.9, color='goldenrod', label='{}'.format(tot['names'][13]))
ax1.plot(extended_wavel[1], extended_spectrum[1]+1.44, lw=1.9, color='orange', label='{}'.format(tot['names'][1]))
ax1.plot(extended_wavel[3], extended_spectrum[3]+2.04, lw=1.9, color='darkorange', label='{}'.format(tot['names'][3]))
ax1.plot(extended_wavel[11], extended_spectrum[11]+2.02, lw=1.9, color='orangered', label='{}'.format(tot['names'][11]))
#EXOGENIC
ax1.plot(extended_wavel[2], extended_spectrum[2]+2.46, lw=1.9, color='forestgreen', label='{}'.format(tot['names'][2]))
ax1.plot(extended_wavel[0], extended_spectrum[0]+2.18, lw=1.9, color='yellowgreen', label='{}'.format(tot['names'][0]))
#CHLORIDES
ax1.plot(extended_wavel[7], extended_spectrum[7]+2.69, lw=1.9, color='brown', label='{}'.format(tot['names'][7]))
ax1.plot(extended_wavel[14], extended_spectrum[14]+3.26, lw=1.9, color='indianred', label='{}'.format(tot['names'][14]))
#WATER AND CO2
ax1.plot(extended_wavel[6], extended_spectrum[6]+3.68, lw=1.9, color='royalblue', label='{}'.format(tot['names'][6]))
ax1.plot(extended_wavel[5], extended_spectrum[5]+3.75, lw=1.9, color='cornflowerblue', label='{}'.format(tot['names'][5]))
ax1.plot(extended_wavel[4], extended_spectrum[4]+3.8, lw=1.9, color='skyblue', label='{}'.format(tot['names'][4]))
#ax1.axvspan(2.7,3.0, facecolor='grey', alpha=0.1)
for i in range(len(sp_feat)):
    ax1.axvline(sp_feat[i], linestyle='--', linewidth=0.8, color='grey', alpha=0.6)
ax2.set_xlim(ax1.get_xlim())
ax2.set_xticks(new_tick_locations, labels=sp_feat, rotation=90)

ax1.text(0.88, -0.05, "Black", c='dimgrey', va='center', ha='right', size='large')
ax1.text(0.88, 0.01, "White", c='snow', va='center', ha='right', size='large')
###
ax1.text(0.88, 0.12, "$NaHCO_3$\n(sodium bicarbonate)", c='darkmagenta', va='center', ha='right',size='large')
#ax1.text(5.1, 0.12, "$NaHCO_3$\n(sodium bicarbonate)", c='darkmagenta', va='center', size='large')
ax1.text(0.88, 0.94, "$Na_2CO_3\cdot10H_2O$\n(natron)", c='mediumvioletred', va='center',ha='right', size='large')
ax1.text(0.88, 1.17, "$Na_2CO_3\cdot1H_2O$\n(thermonatrite)", c='deeppink', va='center',ha='right', size='large')
ax1.text(0.88, 1.38, "$Na_2CO_3$\n(natrite)", c='hotpink', va='center',ha='right', size='large')
###
ax1.text(0.88, 1.66, "$Na_2SO_4\cdot10H_2O$\n(mirabilite)", c='goldenrod', va='center',ha='right', size='large')
ax1.text(0.88, 2.2, "$MgSO_4\cdot6H_2O$\n(hexahydrite)", c='orange', va='center',ha='right', size='large')
ax1.text(0.88, 2.46, "$MgCl_2\cdot6H_2O$\n(bischofite)", c='darkorange', va='center',ha='right', size='large')
ax1.text(0.88, 2.8, "$Na_2Mg(SO_4)_2\cdot4H_2O$\n(bloedite)", c='orangered', va='center',ha='right', size='large')
##
ax1.text(0.88, 3.22, "$H_2SO_4\cdot8H_2O$\n(hydrated sulfuric acid)", c='forestgreen', va='center',ha='right', size='large')
ax1.text(3.97, 2.34, "$H_2O_2$\n(hydrogen peroxide)", c='yellowgreen', va='center', size='large')
###
ax1.text(0.88, 3.6, "$NH_4Cl$\n(ammonium chloride)", c='brown', va='center', ha='right',size='large')
#ax1.text(5.1, 2.74, "$NH_4Cl$\n(ammonium chloride)", c='brown', va='center', size='large')
ax1.text(4.72, 3.36, "$NaCl\cdot2H_2O$\n(hydrohalite)", c='indianred', va='center', size='large')
#ax1.text(5.1, 3.28, "$NaCl\cdot2H_2O$\n(hydrohalite)", c='indianred', va='center', size='large')
###
ax1.text(0.88, 4.27, "$H_2O$ ice 200$\mu$m", c='royalblue', va='center',ha='right', size='large')
ax1.text(0.88, 4.6, "$H_2O$ ice 20$\mu$m", c='cornflowerblue', va='center',ha='right', size='large')
ax1.text(0.88, 4.8, "$CO_2$ ice", c='skyblue', va='center',ha='right', size='large')
plt.show()
```
