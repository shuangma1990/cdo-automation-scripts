### This script is a prototype of ERA5_downloader developed to download climate data from the latest Copernicus Climate Data Store (CDS). There are several advanced features:

1. **Concurrency**: It utilizes Python’s `concurrent.futures` to handle multiple downloads simultaneously, significantly speeding up the process when downloading large datasets.
2. **Robustness with Retry Logic**: A custom retry mechanism with exponential backoff ensures that failed download attempts are retried up to five times, making the script resilient to network or server issues.
3. **File Integrity Check**: After downloading, it verifies the integrity of the NetCDF files using `xarray`, ensuring only complete and valid files are retained.
4. **Logging for Error Monitoring**: Integrated logging tracks each download attempt, providing clear records for troubleshooting and monitoring.
5. **Configurable Automation**: The script allows flexibility through command-line arguments, enabling the user to specify variables, time range, and output location. It also prevents redundant downloads by skipping already existing files.

This combination of features makes the script highly efficient, reliable, and easy to use for large-scale climate data downloads.

## Steps to download ECMWF new CDS data using API (or migration from old to the new CDS)
1. Sign up at Copernicus Climate Data Store (CDS) if you haven't already. Log in.
2. The API requires a python 3.11 environment, run this code in the python interface to confirm your python version:\
`import sys`\
`print(sys.version)`\
Preferably, creating a virtual environment just for ERA5 downloader can avoid version conflict issues:
`conda create --name era5_env python=3.11`\
`conda activate era5_env`\
`conda deactivate	 #if you want to return to base environment once you’re done`
## Install the cdsapi Python package for automated data retrieval:
`pip install 'cdsapi>=0.7.2'`
## Setup the CDS API personal access token.
After logging into your CDS account, navigate to your profile page (click on your name in the top right and select API key).
Copy this information and store it in a file called ~/.cdsapirc in your home directory, <unser_id> is no longer needed compared to the old format\
`url: https://cds-beta.climate.copernicus.eu/api`\
`key: <api_key> `
## Edit and run ERA5_downloader_example.py. Follow the four steps in the beginning of the script.
eg. You can choose the dataset source, months, hours, output folder names, variables, start year, end year, max_workers (give it a 5 to not overwhelm the CDS server)
The script will submit your requests and download it for you when it’s ready
I find out that if your total request number exceed 10 (including queued, in progress, complete, failed), your queued request will not turn into in progress. I deleted a few requests that’s complete, the next queued request quickly went into ‘in progress’ stage.  
For more information, check it out here: https://cds.climate.copernicus.eu/how-to-api and here: https://confluence.ecmwf.int/display/CKB/Please+read%3A+CDS+and+ADS+migrating+to+new+infrastructure%3A+Common+Data+Store+%28CDS%29+Engine 

# quick steps to use this script:
1. Navigate with keyword 'optional' to switch setups upon your need
2. After your edits, rename the file if needed
3. Convert .ipynb to .py: `jupyter nbconvert --to script ERA5_downloader_2mTemp.ipynb`\ (navigate to file dir first)
4. In your terminal, run: 
   `python ERA5_downloader_2mTemp.py --output_dir /Volumes/easystore/ERA5_single_level_2mTemp/ --variables 2m_temperature --start_year 1961 --end_year 1990 --max_workers 6`
   where --outputdir is destination to download the data, --variable is ERA5 climate variables of interest and can be single or multiple, 
   the script will submit a request and save in one .nc file for each year between the --start_year and --end_year 
   --max_workers is the number of requests to the server submitted at a time.
   the script will automatically search for years that already exist in your destination directory, 
   avoid having to download the same file again, if the previous API call got intterupted and have to run the script another time.
