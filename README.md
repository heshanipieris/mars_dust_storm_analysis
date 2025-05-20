# mars_dust_storm_analysis

This repository contains the code and data used to generate the figures and results in the journal article on thermal precursors of Mars dust storms.

---

##  Figure 1 – Median Climatology of Retrieved Surface Temperatures

- Use data from `/data/surface_temp_data/`  
  with the notebook: `/code/Median_climatology_surf_temps_MCSv6.ipynb`
  
- For surface flux climatology, use data from `/data/surface_flux_data/`  
  with the notebook: `/code/Median_climatology_surf_flux.ipynb`

---

##  Figure 2 – Surface Flux Difference Maps

- Use the notebook: `/code/Figure2_notebook.ipynb`  
  with data from `/data/surface_flux_data` and `/data/ls_lat_data`
- The difference DataFrames (`difference_flux_29_multiscattering` etc.) are created by subtracting the surface flux median climatology from individual Mars year flux arrays.

**Example calculation for 2D difference array**:
```python
diff_29_median = np.load('MY29_surf_flux_from_Montabone_multiscattering.npy') - 
                 np.load('median_climatology_array_surfflux_3pm_multiscattering.npy')

np.save('surf_flux_diff_29_median_3pm_multiscattering.npy', diff_29_median)
```
**Example calculation for difference DataFrame**:
```python
diff_T_29 = diff_29_median.T
d_final_29 = {'ls': np.load('final_ls_array_montabone.npy'), 'Latitude': np.load('final_latitude_array_montabone.npy'), 'flux_diff': diff_T_29.flatten()}
df_final_29 = pd.DataFrame(d_final_29)
df_final_29.to_pickle('difference_flux_29_multiscattering')
```
- Apply the same method for generating dust optical depth difference arrays and DataFrames. Follow notebook for next steps.

---

##  Figure 3 – Surface Temperature Difference Maps

- Use the notebook: `/code/Figure3_notebook.ipynb`  
  with data from `/data/surface_temp_data/` and `/data/ls_lat_data/`
- Then follow the same approach as described for Figure 2.

---

## Figure 4 - Spearman Correlation between Surface Flux and Surface Temperature 

- Use the following function:
  ```python
  def flux_temp_correlation(time_series1, time_series2, MY, lband):
    
    time_series1 = pd.Series(time_series1)
    time_series2 = pd.Series(time_series2)
    spearman_corr = time_series1.corr(time_series2, method='spearman')
      
    print('MY {}, lat band {} correlation = {}'.format(MY, lband, spearman_corr))
    
    return(spearman_corr)
  ```
- Here, `time_series1` and `time_series2` correpond to the line plots of solar surface flux and surface temperature
- These line plots are the gaussian smoothed versions that are found in `/Figure2_notebook` and `/Figure3_notebook` as `gauss_array_29_temp` and `gauss_array_29_flux` etc.
- To calculate the correlations across five latitudinal bands from -90&deg; to 60&deg; change `biny` into 5 seperate bins as `np.arange(0, 40, 30)`, `np.arange(30, 70, 30)` and so on.

---

## Figure 5 & 6 - Cross-Correlation coefficient for 'A' & 'C' storms

- Similarly to Figure 4, this too will be using the gaussian-smoothed line plots found in `/Figure2_notebook`
- Correlation is once again stratified across five latitudinal bands from -90&deg; to 60&deg;.
- Then use `find_peaks` function from `scipy.signal` as in following example:
  ```python
  peak_indices_flux_30 = [find_peaks(gauss_array)[0] for gauss_array in gauss_arrays_flux_30]

  peak_indices_od_30 = [find_peaks(gauss_array)[0] for gauss_array in gauss_arrays_od_30]
  ```
- Here `_30` refers to the latitudinal band from 0&deg; to 30&deg;. Repeat for other bands.
- Use the following function to find the lag between peak in surface flux and peak in dust storm:
  ```python
  def find_flux_storm_lag(storm_ls, flux_peak_indices, od_peak_indices):
    
    #storm_ls = location of storm peak in solar longitude
    ls_to_plot = np.arange(175.5, 275, 1) #Ls range for A storms
    flux_peaks = ls_to_plot[flux_peak_indices]
    od_peaks = ls_to_plot[od_peak_indices] #storm peaks
    ls_all_peaks = np.sort(np.concatenate((flux_peaks, od_peaks))) #create one continuous array for flux and storm peaks
    storm_idx = np.argwhere(ls_all_peaks == storm_ls) 
    flux_ls = ls_all_peaks[storm_idx - 1]
    lag = storm_ls - flux_ls
    return(lag.item())
  ```
- Then use the cross-correlation function from `statsmodels.api` as:
  ```python
  def cross_coeffs(opacity, flux):

    coeffs = sm.tsa.stattools.ccf(opacity, flux, adjusted=False, fft=False)

    return(coeffs)
  ```
- Next find the corresponding cross-correlation coefficient from these results for the lags found earlier.
- Repeat process for 'C' storms for `binx = np.arange(280, 361, 1)`

---

## Figure 7 - Probability Density of Precursor Flux Peaks 

- Use `find_peaks` from `scipy.signal` with parameter `height` set to 1.2 (for threshold of 1.2 W/m<sup>2</sup>).

--- 

## Figure 8 - CAPE lineplot comparison

- Use the example notebook `/code/Figure8_and_TempProfiles.ipynb`
  with data from `/data/temp_profile_data`
- Retrieve `MY30_CAPE_sorted` file saved at bottom of notebook for all Mars years.
- Then compute the median CAPE climatology following the directions for Figure 1.
- Calculate the difference 2D arrays and difference DataFrames as instructed for Figures 2 & 3.
- Derive the CAPE lineplots following the code in `/code/Figure3_notebook.ipynb`, modified with CAPE data.

--- 

## Figure 9 - Spearman correlation between surface insolation and CAPE 

- Use the CAPE line plot data derived for Figure 8 and follow instructions for Figure 4

---

## Figure 10 - Spearman correlation between surface insolation and dust devil activity 

- Use the example notebook `/code/Figure8_and_TempProfiles.ipynb`
  with data from `/data/temp_profile_data`
- Retrieve `MY30_dustdevil_sorted` file saved at bottom of notebook for all Mars years.
- Then compute the median dust devil climatology following the directions for Figure 1.
- Calculate the difference 2D arrays and difference DataFrames as instructed for Figures 2 & 3.
- Derive the dust devil lineplots following the code in `/code/Figure3_notebook.ipynb`, modified with dust devil data.
- Use these lineplots along with instructions under Figure 4.

