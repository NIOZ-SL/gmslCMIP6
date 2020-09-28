# gmslCMIP6
This repository can be used for pulling CMIP6 data from ESGF, preprocessing raw (monthly mean, but probably also other frequencies) 'zostoga' and 'tas' data, including time-merging, dedrifting and calculating area-weighted means, and analyzing the preprocessed data.

Instructions for running the WGET script can be found here: https://esgf.github.io/esgf-user-support/user_guide.html#download-data-from-esgf-using-wget

Required Python packages: xarray, numpy, fnmatch, cdo
