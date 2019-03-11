## How to organize data and run code

### Organizing your image data

The standard organization structure for the input image data is specified below, utilizing the example of (FISH_IF) nanog_brd4. Each biological/technical replicate data set can have any identifier, as long as the last two digits represent a unique number for e.g. E01 or Exp01 or Exp_01. These last two numbers are used as the unique identifiers for each image set. This is the unique number that will be used to look for csv file of manually curated FISH/random foci if that is provided as an input. The format of these manually curated files are given below. Further, all strings are character sensitive.

Within each replicate, the input_channel identifier for FISH/IF are used to identify the respective images.

A typical folder structure is listed below, where the manually curated FISH foci (under Foci_calls) and manually generated control locations (under Random_foci) are provided as input. In general, the program can generate both of the above types of data on it's own.

* nanog_brd4
	* Images
		* 	E01
			* nanog_brd4_01......._405_DAPI.TIF
			* 	nanog_brd4_01......._488_GFP.TIF
			* nanog_brd4_01........_561_CY3.TIF
		* E02
			* nanog_brd4_02......._405_DAPI.TIF
			* 	nanog_brd4_02......._488_GFP.TIF
			* nanog_brd4_02........_561_CY3.TIF
		.....................
		* E12
			* nanog_brd4_12......._405_DAPI.TIF
			* 	nanog_brd4_12......._488_GFP.TIF
			* nanog_brd4_12........_561_CY3.TIF

	* Foci_calls
		* 	nanog_brd4_01.csv
		.......
		* nanog_brd4_12.csv

	* Random_foci
		* 	nanog_brd4_01.randomPixels.txt
				...........
		* nanog_brd4_12.randomPixels.txt



## Running the code

Simply run *call_3D_analysis_code.m* for the code to run. A set of minimum parameters need to be defined:

* directory_name	-->	This should point to the 'Images' folder, for e.g. 'nanog_brd4/Images/'

* output_dir_name	-->	This is the folder under which external files will be stored. For example, if named 'test', the output data and images will be stored under 'nanog_brd4/test', and if a random foci have been called, they will be stored under 'nanog_brd4/test_random'


* in_params.FISH_name	--->	This variable stores the name of the RNA FISH, to use it for titling plots

* in_params.IF_name	--->	This variable stores the name of the IF protein, to use it for titling plots

* in_params.FISH_channel	--->	This variable stores the string of wavelength of FISH channel, for example in the example system mentioned above, the FISH_channel = '561'. This is used to identify the relevant image files while running the analysis. Please ensure that this value is appropriately specified, otherwise, it will call foci in the IF or DAPI channels, which could cause the program to crash or hang.

* in_params.FISH_channel	--->	This variable stores the string of wavelength of IF channel, for example in the example system mentioned above, the IF_channel = '488'. This is used to identify the relevant image files while running the analysis.


Additional details and intricacies about the code will be updated often.

## Formatting manually curated foci

The manually called foci are typically present under a subfolder called Foci_calls (as referenced above). Under this folder, csv files with pointers to unique image set ID (01, 02 ,... etc) is present. Examples for the set of brd4(IF) and nanog (FISH) are given above.

*	For example if the FISH foci name has number 12, like prdm12, please make sure that you don't name the manually called or randomly called foci file with 12 in it because it will confuse with uniq folder id. Good roundabouts include prdm_1_2 or prdm_twelve. This can be easily avoided if add stringent constraints on where the unique identifier will appear, or make it more than a number,or a 'longer' number! This update is arriving shortly.

Each CSV file should have the following columns:

*	Volume,Surface,Intensity,Centre X,Centre Y,Centre Z,Centre int X,Centre int Y,Centre int Z


Columns 4,5, & 6 are read to as X_center, Y_center, and Z_center. Other columns can all be 0's, but must be present. This is because this standard file format is that outputted by Fiji/Image J.

### Parsing output data
If the output folder defined is foo, two output folders foo and foo_random will be created under the parent folder where the Images subfolder exist. Further, if random_auto_call is set to 1, then a subfolder Random_foci_auto will also be created with a list of CSV files of randomly called nuclear foci.

Under foo/Combined_data/, the following files are created:
* **nanog_brd4_production_average_image** (fig,svg) - Average 2D projection plot of FISH/IF colocalization around FISH centroid. (typically +/- 5 stacks)

* **nanog_brd4_production_IRF_image** (fig,svg) - Average 3D IRF plot of FISH/IF colocalization

* **nanog_brd4_foci_statistics.csv** - File with statistics on individual foci - Centroid, Average FISH/IF signal close to centroid, and source image file (replicate ID)

* **Total_data.mat** - Raw matrices of Image and IRF data under:

    * FISH_data/IF_data - Average 2D projection data of size (N*N*R), where N is the length scale (in pixels) assayed and R - number of replicates

    * FISH_IRF/IF_IRF - IRF data stored in a cell array (R*1), where each cell contains the dist function (bins of distance in um), and intensity arrays.

    * image_data_set - The file ID of the input data set.

    * input_params - The complete listing of all input parametrization values is found under this variable
