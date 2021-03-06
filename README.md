# gmslCMIP6
This repository contains several scripts that were used for the results of:

```
Hermans, T. H. J., Gregory, J. M., Palmer, M. D., Ringer, M. A., Katsman, C. A., & Slangen, A. B. A. (2021). Projecting global mean sea‐level change using CMIP6 models. Geophysical Research Letters, 48, e2020GL092064. https://doi.org/10.1029/2020GL092064
```

Please cite our manuscript when using our scripts for future work. The manuscript is premised on global thermal expansion (GTE) and global surface air temperature (GSAT) data from CMIP6 models. This repository can be used for pulling CMIP6 data (in this case, global mean thermosteric sea-level change 'zostoga', gridded surface air temperature 'tas' and atmosphere cell area 'areacella') from ESGF using WGET, preprocessing raw (monthly mean intended, but possibly also other frequencies) 'zostoga' and 'tas' data, including time-merging, dedrifting and calculating area-weighted means, and analyzing the preprocessed data. The underlying data of the manuscript is available at: http://doi.org/10.4121/12958079.

# Downloading with WGET
Instructions for running the WGET scripts can be found here: https://esgf.github.io/esgf-user-support/user_guide.html#download-data-from-esgf-using-wget. For example, using

```
bash wget-20200715133532_zostoga.sh
```

will pull all 'zostoga' variant files available at the time of creation of the WGET script with realization=1 from ESGF and store them per variable per model, as indicated by the download structure variable in the Search URL. Note that a 5 second delay can be built in with 'sleep 5' between each download, to avoid overasking certain ESGF nodes. The script may require a few iterations to completion, depending on server response and hiccups for particular models. FIO-ESM-2-0 SSP experiments repeatedly fail checksums (07-10-20) and could be removed from the WGET script manually. Similar, updated WGET scripts may be generated by issueing the RestFull API URL ('Search URL' in the WGET scripts) in your browser. 

# Converting 'tas' into GSAT
The script *cmip6_save_areawmean_atmos.py* loops over each model and experiment, and converts a timeseries of a 2D gridded atmospheric variable such as 'tas' into an global area-weighted mean timeseries. This requires the gridded data as well as area cell weights (CMIP6 'areacella' (atmosphere), 'areacello' (ocean)), which can be downloaded using WGET as well.

# Dedrifting 'zostoga'
The dedrifting scripts loop over each model and experiment. The input is expected to be time-merged (e.g., using *cmip6_merge_raw_timechunks.py*), and organized per model directory. Two flavours of dedrifting using piControl experiments are available: 1) linear dedrifting, which fits a linear polynomial to the full piControl time series, and subtracts that from the historical and SSP experiments, 2) quadratic dedrifting, which fits a quadratic polynomial to the full piControl time series, and subtracts the **overlapping** part of the piControl run from historical and SSP experiments. 2) requires additional information on the branch times of historical in piControl, and ssps in historical. This information is available from metadata, but is for some models inconsistent, and so less generic than 1). 1) yields small differences to 2) compared to any forced signal over the 21st century (Hermans et al., 2021, submitted to GRL).  

# Pooling model data into a multi-model ensemble
The script *zostoga_tas_ens_to_netcdf.py* is included to pool the dedrifted 'zostoga' and global area-weighted mean 'tas' historical and SSP experiments for each model into a mluit-model ensemble. The logic applied here, which could be adapted as the user sees fit, is to include only models that have both variables, for both experiments, for each of the desired and configurable emissions scenarios. Historical and SSP data must have the same grid and variant,and the script stores this information in the meta data. The script appends the historical and scenario runs for a target period, and missing values are filled through interpolation. The time series are referenced to a common base year period. 

# Analysis example
The CMIP6 GTE and GSAT data (available at http://doi.org/10.4121/12958079, or collected through the above downloading and preprocessing procedure), can be plotted using xarray. *plot_GTE_GSAT_CMIP5_CMIP6_qnts_curves.py* demonstrates this for CMIP5 and CMIP6 data, and reproduces Figure 1 of the manuscript. 

---
Required Python packages: xarray, numpy, fnmatch, cdo
