# mars_dust_storm_analysis
Code and data used to generate figures and results in journal article on thermal precursors of Mars dust storms

Figure 1 - Median Climatology of Retrieved Surface Temperatures 

- Use data from /data/surface_temp_data along with /Median_climatology_surf_temps_MCSv6.ipynb from /code 

Figure 2 - Surface Flux difference maps 

- Use /Figure2_notebook.ipynb from /code along with data from /surface_flux_data from /data
- The 'difference_flux_29_multiscattering' etc. dataframes are created by subtracting the surface flux median climatology from individual Mars years
- Example:
  diff_29_median = np.load('MY29_surf_flux_from_Montabone_multiscattering.npy') - np.load('median_climatology_array_surfflux_3pm_multiscattering.npy')
  np.save('surf_flux_diff_29_median_3pm_multiscattering', diff_29_median)
- ^This is the difference 2d array
- Example dataframe
  diff_T_29 = diff_29_median.T
  d_final_29 = {'ls': np.load('final_ls_array_montabone.npy'), 'Latitude': np.load('final_latitude_array_montabone.npy'), 'flux_diff': diff_T_29.flatten()}
  df_final_29 = pd.DataFrame(d_final_29)
  df_final_29.to_pickle('difference_flux_29_multiscattering')
- Use similar method to obtain dust optical depth difference dataframe and 2d array and follow notebook

Figure 3 - Surface Temperature difference maps 

- Use /Figure3_notebook.ipynb from /code along with data from /surface_temp_data from /data
- Follow same method as written above for Figure 2
