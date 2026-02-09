# UVIT-sensitivity-analysis
The Ultra-Violet Imaging Telescope (UVIT) is one of the five payloads aboard the AstroSat observatory, which was launched on September 28, 2015. UVIT can perform observations in the visible (VIS), far-ultraviolet (FUV), and near-ultraviolet (NUV) channels using a set of different filter configurations. As of September 2024, UVIT has completed 9 years in orbit. The objective of this study is to probe for any variation in the sensitivity of UVIT. For this purpose, we studied the observations taken by UVIT of the white dwarf HZ 4 and open cluster NGC 188. HZ 4 was used as a single-source reference, while three candidate probes were selected from the NGC 188 field.


## Repository contents
This study uses Level-2 (L2) UVIT pipeline data, publicly available from the AstroSat Archive at the Indian Space Science Data Center (ISSDC). The datasets were analysed using scripts written in Python. This repository contains the UVIT observation datasets for HZ 4 and NGC 188 (stored as .tar.gz files), a Jupyter notebook `uvit_sensitivity.ipynb` for the sensitivity analysis, and supporting data files. <br>
$\textbf{Important:}$ To run the notebook, the data contents from the .tar.gz files must be extracted in the same directory as the notebook. After downloading the repository contents and extracting the .tar.gz, files, the following directory tree will be obtained (up to one level of subdirectories):

``` bash
├── aligned_wcs_master_FUV_F1_events_list_image.fits.gz
├── aligned_wcs_master_NUV_F6_events_list_image.fits.gz
├── calibfile.fits
├── coloured_image_candidates.png
├── coloured_image_NGC188.png
├── HZ4
│   ├── Data_astrobrowse
│   │   ├── AS1C02_002T01_9000000888
│   │   │   ├── uvt_05_AS1C02_002T01_9000000888uvtFIIPC44F1I_l2img.fits.gz
│   │   │   ├── uvt_05_AS1C02_002T01_9000000888uvtFIIPC44F1_l2ce.fits.gz
│   │   │   :
│   │   :
│   ├── Data_info
│   │   ├── HZ4_field_center_coordinates.csv
│   │   └── obs_id.txt
│   ├── source_detection
│   ├── aperture_photometry
│   ├── source_detection_HZ4.py
│   ├── aperture_photometry_HZ4.py
│   └── source_HZ4_cps.csv
├── NGC188
│   ├── Data_astrobrowse
│   │   │   ├── uvt_05_AS1T01_034T01_9000000392uvtFIIPC00F1I_l2img.fits.gz
│   │   │   ├── uvt_05_AS1T01_034T01_9000000392uvtFIIPC00F1_l2ce.fits.gz
│   │   │   :
│   │   :
│   ├── Data_info
│   │   ├── NGC188_field_center_coordinates.csv
│   │   └── obs_id.txt
│   ├── source_detection
│   ├── aperture_photometry
│   ├── coord_cross_match
│   ├── source_detection_NGC188.py
│   ├── aperture_photomtery_NGC188.py
│   └── coord_cross_match_NGC188.py
├── master_sheets
│   ├── candidate_1.csv
│   ├── candidate_2.csv
│   ├── candidate_3.csv
│   └── HZ4.csv
├── plots
│   ├── median_bkg_NGC188_HZ4.png
│   ├── senstivity_variation_HZ4.png
│   └── senstivity_variation_NGC188.png
└── uvit_sensitivity.ipynb
```

### Observation data (HZ 4 and NGC 188)

Before performing the UVIT sensitivity analysis, the episode image data for HZ 4 and NGC 188 underwent a series of initial analyses. These included detecting potential sources in the field, performing aperture photometry, and identifying sources across all episode images. The HZ 4 and NGC 188 directories contain the full analysis workflow for each field, along with the required Python scripts. <br>

A breakdown of the directory contents is provided below:

- Data_astrobrowse:
  - `uvt*F1I_l2img.fits.gz` : Episode image in instrument coordinate system in the F1 (F148W) filter for each observation ID
  - `uvt*F1_l2ce.fits.gz` : Corrected events list in the F1 (F148W) filter for each observation ID

- Data_info:
  - `*field_center_coordinates.csv` : Coordinates of the image field center in subpixels
  - `obs_id.txt` : List of observation IDs of UVIT L2 datasets used in this study


### Python scripts used for HZ 4 and NGC 188 datasets

The scripts must be executed in the following sequence:

1. `source_detection.py` \
The script performs background estimation, and source detection on each of the episode images using the DAOFIND method. It creates a directory, ‘`~/source_detection/` and saves the output separately in sub-directories for each observation ID. The following outputs are generated for every episode image:
   - a .dat file containg the UVIT pointing information ($\alpha$, $\delta$) in degrees ;
   - a .dat file containing the centroid information of the potential sources ;
   - a .png file of the rescaled image ;
   - a .png file of the 2D background map ; and
   - a .png file depicting the detections. <br>
The code also saves the summary of the run in the `source_bkg.txt` file containing the observation ID, episode number, threshold, number of sources detected, and the median background value (CPS).

2. `aperture_photomtery.py` \
The script performs aperture photometry on each of the episode images. It creates a directory, ‘`~/aperture_photometry/`’ and saves the output separately in sub-directories for each observation ID. The following ouptut is generated for every episode image:
   - a .csv file (`*aperture_CPS.csv`) containing the aperture sum and the associated error for the detected sources having centroids ‘xcenter’ and ‘ycenter’
   - a .csv file (`*FF_vals.csv`) containing the source coordinates in the 512x512 flat-field pixels, along with the average pixel values in a 21x21 pixel window around the source
As the source flux obtained incorporates the flat-field corrections, this adjustment was reversed using the `calibfile.fits` file.

3. `coord_cross_match.py` \
The script cross-matches the source coordinates in the reference image with the source coordinates in every episode image to identify the coordinates of the selected three candidates across all the NGC 188 episode images. It creates a directory `~/cross_coord_match/` and saves the output separately in sub-directories for each observation ID. The following output is generated for every episode image:
    - a .csv file containing the x and y centroids for the candidates along with the source flux and measured error in CPS units, as represented by the columns, ‘x_center_source’, ‘y_center_source’, ‘source_cps’ and ‘source_err_cps’ respectively. The information is stored in the .csv file in the sequence corresponding to candidates 1, 2, and 3 respectively.<br>
$\textbf{Note:}$ This script was only used for the NGC 188 data. Unlike the crowded NGC 188 field, coordinate cross-matching was unnecessary for the HZ 4 data since the source was isolated. <br>


### Sensitivity analysis
The sensitivity analysis is documented in the Jupyter notebook `uvit_sensitivity.ipynb` and utilises results from the above-mentioned work. The code applies aperture and saturation corrections to the non-flat-fielded source flux. Flat-field corrections were then reintroduced to obtain the final flux with aperture, saturation and flat-field corrections in CPS units. The script generates three master sheets, one for each selected candidate in the NGC 188 field, and one master sheet for HZ 4 source, containing relevant image data information, and saves them as .csv files in the directory `~/master_sheets/`. Subsequently, a weighted least squares regression is performed on both the HZ 4 and NGC 188 candidates to evaluate long-term and short-term sensitivity variations. The resultant plots are saved in the directory `~/plots/`.


### Additional data files
- `aligned_wcs_master_FUV_F1_events_list_image.fits.gz` : Astrometrically solved NGC188 FITS image generated by combining episode events lists in the FUV F1 filter
- `aligned_wcs_master_NUV_F6_events_list_image.fits.gz` : Astrometrically solved NGC188 FITS image generated by combining episode events lists in the NUV F6 filter <br>
For detailed information on combining event lists of UVIT data, please refer to https://github.com/prajwel/curvit.
- `calibfile.fits` : Flat-field information for FUV F1 filter stored in the calibration data file
- `coloured_image_candidates.png`
- `coloured_image_NGC188.png`


If you use this dataset in your research, analysis, or publications, we would appreciate your citation. You can cite it using the DOI https://doi.org/10.5281/zenodo.16036531 .
