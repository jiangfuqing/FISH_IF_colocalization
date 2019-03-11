# 2-channel colocalization analysis:

## About

This code was employed to perform analysis and generate visualization
for studying colocalization b/w FISH & IF channels for figures reported in [Sabari & D'allAgnese et al., 2018](https://10.1126/science.aar3958) and [Boija & Klein et al., 2018](https://10.1016/j.cell.2018.10.042).

### Dependencies:

* MATLAB (Version 2016 or higher)

### Code structure

* The main file to run is call_3Danalysis_code.m

* params.m allows one to modulate certain key parameters (not exhaustive):
    * Account for curated foci
    * Size of the image data set to collect
    * Microscope parameters
    * Centroid stitching and foci acceptance criteria
    * Automated foci detection criteria

* All other detailed computing functions are found under source_files/, and can be starting point for further development.

* Refer [guide](guide.md) for tips on organizing data and employing code.


### Code functions:

This code **broadly** has two aims:

* Identify foci in one channel (typically FISH - with better image quality)

* Around the FISH foci, calculate the following observables in the IF channel
    * Average image analysis of the FISH data and IF data

    * Intensity distribution of the IF channel around the FISH foci.

* These steps are repeated at randomly located nuclear positions (within the DAPI-positive channel) to obtain a control data-set. The random nuclear positions can be generated automatically or provided by the user.

**Note**-  If you wish to perform random control based on the DAPI channel to identify control nuclear positions, make sure those channels are included in the input data files!


### How to run code

#### Overall analysis

Simply run *call_3D_analysis_code.m* for the code to run. A set of minimum parameters need to be defined:


* directory_name	-->	This should point to the 'Images' folder, for e.g. 'nanog_brd4/Images/'

* output_dir_name	-->	This is the folder under which external files will be stored. For example, if named 'test', the output data and images will be stored under 'nanog_brd4/test', and if a random foci have been called, they will be stored under 'nanog_brd4/test_random'


* in_params.FISH_name	--->	This variable stores the name of the RNA FISH, to use it for titling plots

* in_params.IF_name	--->	This variable stores the name of the IF protein, to use it for titling plots

* in_params.FISH_channel	--->	This variable stores the string of wavelength of FISH channel, for example in the example system mentioned above, the FISH_channel = '561'. This is used to identify the relevant image files while running the analysis. Please ensure that this value is appropriately specified, otherwise, it will call foci in the IF or DAPI channels, which could cause the program to crash or hang.

* in_params.FISH_channel	--->	This variable stores the string of wavelength of IF channel, for example in the example system mentioned above, the IF_channel = '488'. This is used to identify the relevant image files while running the analysis.

#### Deeper dive into setting parameters
Many other important (thresholding/Microscope/pixel size for e.g.) parameters are set in params.m. They are currently set to values that have been optimized for studies re: FISH-IF experiments done in mESCs. These are variables of the **input_params class**, and include:
- **threshold_multiplier** - Multiplicative threshold for intensity thresholding
- **FISH_threshold** - Minimum area (in pixels) for thresholding
- **xpixel** - Pixel length (um) along x/y directions
- **zpixel** - Pixel length (um) along z stack
- **distance_threshold** - Pixel displacement (um) across z-stacks of centroid that is permissible for stitching
- **automatically_call_foci** - Flag variable that decides whether (1)
or not (0) automatic foci detection should happen. Default setting
is ON (1).
- **curate_called_foci** - Flag variable that decides whether (1)
or not (0) automatically called foci are curated across a manually
provided csv list of foci. Default setting is OFF (0).
- **volume_threshold** - Minimum volume (um^3) for accepting foci.
Default setting is 0.05 um^3 <-- experience for FISH data in mESCs.
- **size_box** - Half length of box around FISH centroid (in pixels)
for which the IF data is gathered. Default is 25 pixels ~ 1.5 um.
- **random_auto_call** - Flag variable that decides whether (1)
or not (0) to call random foci based on DAPI channel data. Default
setting is ON (1) - requires 405 channel image data.

### Parsing output data
If the output folder defined is foo, two output folders foo and foo_random will be created under the parent folder where the Images subfolder exist. Further, if random_auto_call is set to 1, then a subfolder Random_foci_auto will also be created with a list of CSV files of randomly called nuclear foci.

Under foo/Combined_data/, the following files are created:
- **nanog_brd4_production_average_image** (fig,svg) - Average 2D projection plot of FISH/IF colocalization around FISH centroid. (typically +/- 5 stacks)
- **nanog_brd4_production_IRF_image** (fig,svg) - Average 3D IRF plot of FISH/IF colocalization
- **nanog_brd4_foci_statistics.csv** - File with statistics on individual foci - Centroid, Average FISH/IF signal close to centroid, and source image file (replicate ID)
- **Total_data.mat** - Raw matrices of Image and IRF data under:
  - FISH_data/IF_data - Average 2D projection data of size (N*N*R), where N is the length scale (in pixels) assayed and R - number of replicates
  - FISH_IRF/IF_IRF - IRF data stored in a cell array (R*1), where each cell contains the dist function (bins of distance in um), and intensity arrays.
  - image_data_set - The file ID of the input data set.
  - input_params - The complete listing of all input parametrization values is found under this variable
