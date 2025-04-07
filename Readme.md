
# **Workflow Template for Running C-Motion Code Files**

## **Overview**

This document provides a structured workflow to execute C-Motion files in Python notebooks (Google Colab) and MATLAB. The analysis involves NAICS/SIC industry codes and Metropolitan Statistical Areas (MSAs). Follow the steps below to ensure accurate execution and reproducible results.

---

## **Step 1: Identify Industry and MSA for Study**

1. **Define Scope**:
    - **Broad Scope**: Identify the broad industry sector of interest  such as steel, aerospace, motor vehicles, entertainment, etc.  Identify geographical areas (state/region/MSA) where these sectors have shown growth and decline over the time period 1974 - 2022. 
    - **Narrow Scope**: Identify specific industries dependent on the primary industry identified. Use a product such as motor vehicle for example, and identify the NAICS code. 
	    - Using the following link as a ballpark to identify possible SIC codes and dependent industries codes: https://www.naics.com/naics-to-sic-crosswalk-2/. 
	    - Use the Census crosswalks to confirm portability among SIC and NAICS codes from the following link: https://www.census.gov/topics/employment/industry-occupation/guidance/code-lists.html 
	    
	- **Industry Code Reference Table**:  The following table illustrates the time period and relevant codes:

  | **Year Range** | **Classification System** |
| :------------- | :------------------------ |
| 1974–1996      | SIC (Standard Industrial Classification) |
| 1997–2001      | NAICS 1997               |
| 2002–2006      | NAICS 2002               |
| 2007–2011      | NAICS 2007               |
| 2012–2016      | NAICS 2012               |
| 2017–2022      | NAICS 2017               |

    
2. **Select NAICS/SIC Codes**:
    - Use the table below to document selected codes. The Primary Industry column is to help ascertain the scope, most often the industry code will be directly connected and hence the primary industry. 
    
| Primary Industry | SIC Code & Description | NAICS Code & Description  |
| :-- | :-- | :-- |
|  |  |  |
|  |  |  |

3. Identify a few MSA's of interest, listing out the MSA code and Name. If possible, identify major events relevant to the MSA, such as plant opening/closures and the year in which it took place.

---

## **Step 2: Run Crosswalks for Industry and MSA in Google Colab**

1. Open the provided Google Colab notebooks: First, `1_Crosswalks-Industry.ipynb` and `2_Crosswalks-MSA.ipynb` next. 
3. Follow instructions provided in the markdown under Google Colab notebooks. Ensure the following:
	- All data files are downloaded and directories on google drive created as per instructions.
	- Industry, MSA codes, and, column numbers are substituted in the code appropriately. 
	- File paths and file names are modified as needed.
4. Sequentially run the following python files:
 - [ ] **Step 1**: `1_Crosswalks-Industry.ipynb`. This code requires previously tabulated SIC/NAICS codes to be entered in the code. The output "CombinedFiles.csv" aggregates establishment counts from County Business Patterns data for selected industry codes over the time period 1974-2023.
  - [ ] **Step 2**: `2_Crosswalks-MSA.ipynb`. This requires users to enter the MSA codes and Names identified earlier in addition to industry codes. Two output files and corresponding plots are generates: 
	    - Aggregate establishments over all MSA's for specific industry codes over time.
	    - Aggregate establishments over each MSA for specific codes over time.
   - [ ] **Step 3**: Analyze plots generated eyeballing for any sudden peaks and troughs. Look for following patterns: 
        - Do the spikes in data correlate with NAICS/SIC code changes? These are indicated with dotted lines in the plot and corresponds to [Industry Code Reference Table](#Link) from earlier.
5. Use the table below to track files and modifications:

| File Name | Description | MATLAB Input  | Date Updated|
|  :-- | :--  | :-- | :-- |
| `CombinedFile.csv`  | Annual Establishment counts for each industry code | No  | mm/dd/yyyy
|  `est_industry_year` | Annual Aggregate Establishment Counts | Yes, Stage 1 | mm/dd/yyyy  |
| `est_industry_year_cbsaxxxxx` | Annual Aggregate Establishment Counts for CBSA/MSA xxxxx |Yes, Stage 1 | mm/dd/yyyy

---

## **Step 3: Run Stage 1 of C-Motion (Monte Carlo Simulation) in MATLAB**

 1. **Prepare to run MATLAB files**
	MATLAB uses relative paths for file references. Files without explicit paths are assumed to be in the same directory as the script. To access files in other directories:
	```matlab
	% Access a file in a subdirectory located in a folder one level above
	filename = fullfile('directoryname', 'filename.xlsx');
	data = readtable(filename);
	% Or use like this 
	data = readtable(dir('directoryname/filename.xlsx')
	```
 2. **Ensure required input files and directories are available:**	
	 - [ ] `pop_year_cbsa.csv` : Population per MSA from Census. 
	  -   Ensure population data covers all years under study
	  -   For shorter time periods, remove rows for years not needed
	  -   For periods beyond 2016, use the provided population file
	 - [ ] [`est_industry_year.xlsx`](#previous) from crosswalk output. 
	 -   Place this file in the same directory as the MATLAB script
	 - [ ] [`est_industry_year_cbsaxxxxx.xlsx`](#previous) files from crosswalk output. 
	 -   Create a folder named  `crosswalk_outputs`  in the same location as the MATLAB script
	-   Copy all crosswalk output files into this folder
	-   The code will access these files using:
	 ```matlab
	%% Process multiple CBSA files
	cbsa_files = dir('crosswalk_outputs/est_autocore_year_cbsa*.xlsx');
	```
	- [ ]  Output directories 
	-   The program will write outputs to  `C:\MatlabStep1_outputs`
	-   If this directory doesn't exist, the program will create it (given proper permissions)
	-   You may also need  `C:\MatlabStep2_outputs`  for later steps
	-   The code creates directories as needed:
	 ```matlab
	 %% Initialize output directory
	outputDir = 'C:\MatlabStep1_outputs';
	if ~exist(outputDir, 'dir')
		mkdir(outputDir);
	end
	 ```
	 
 3. **Run the MATLAB script**
	- Open `Step 1 - Concentration_levels_seed0_Updated.m` in MATLAB 
	-   Execute the script 
 4. **Verify output files**
	 - [ ]  `Clevel_CBSA_xxxxx.xlsx` files should be created in `C:\MatlabStep1_outputs\CBSA_Files` (where `xxxxx`is the CBSA/MSA code)
	 - [ ] `MC_output_xxxx.txt` files should be created (where `xxxx` is the CBSA/MSA code)
	 
 5. **Check for success messages**  in the MATLAB command window to confirm successful execution
---

## **Step 5: Run Stage 2 of C-Motion (break analysis and dynamics calculations) in MATLAB**

1. **Ensure required input files and directories are available:**
	- [ ]  Stage 1 outputs: `Clevel_CBSA_xxxxx.xlsx` 
	- [ ]   `supp_files`  directory exists one directory level 	 above where your script is located. For example:
		`..MATLAB\Clulster_dynamics.m\Cluster_dynamics.m`
		Then supplementary files are located here:
		`..MATLAB\supp_files`
		MATLAB/
		├── Cluster_dynamics.m/
		│   └── Cluster_dynamics.m    (your main script)
		└── supp_files/               (supplementary files directory)
		    └── [supplementary files]
		Here is the code snippet that looks for this:
		```matlab
		% Directory containing the sub-files
		suppDir = fullfile('..', 'supp_files'); % Move up one directory level
		if exist(suppDir, 'dir')
			addpath(suppDir);
		else
			warning('Supp files directory not found: %s', suppDir);
		end
		```
	- [ ]  Output directories 
		-   The program will write outputs to  `C:\MatlabStep2_outputs`
		-   If this directory doesn't exist, the program will create it (given proper permissions)
	
2. **Modify parameters - Exclude breaks attributable to changes in industry codes** 
	 - [ ]  Change `exception_years`if needed, leave blank otherwise. 
		```matlab
	   % Define exception years
		exception_years = [1997, 2012, 2017];
		
		% Handle exception years
		if ~isempty(daty)
			break_years = start_year + daty - 1;
			valid_breaks = ~ismember(break_years, exception_years);
			daty = daty(valid_breaks);
	   ```

 3. **Run the MATLAB script**
	- [ ]  Open `Step 2 - Cluster_Dynamics.m` in MATLAB 
	- [ ]  Execute the script 
 
 4. **Verify output files**
 Note for the following section: `xxxxx` is the CBSA/MSA code in  following files. All files are created under `C:\MatlabStep2_outputs`:
	 - [ ]  `regression_output_industry_est_cbsaxxxxx.xlsx` 
	 - [ ] Ignore following files: `regression_output_industry_est_cbsaxxxxx.csv` 
	 - [ ] `break_tests_cbsaxxxxx.csv`
	 - [ ] `break_dates_cbsaxxxxx.csv` 
	 - [ ] `BP_log_cbsaxxxxx.txt` 
	 
 5. **Check for success messages**  in the MATLAB command window to confirm successful execution
 
 6. Document key outputs:
 
| **Output File Name** | **Description** |
| :-- | :-- |
| `regression_output_industry_est_cbsaxxxxx.xlsx` | Overall regression results from break analysis|
| `break_dates_cbsaxxxxx.csv` | Break years|
| `break_tests_cbsaxxxxx.csv` | Break test results|
| `BP_log_cbsaxxxxx.mat` | Log file containing all above results|
---

## **Step 6: Analyze Trends in Industry Using Python**
 - [ ]  Open the provided Google Colab notebooks: `ARIM-break-analysis-OLS.ipynb`. 
 - Follow instructions provided in the markdown under Google Colab notebooks. Ensure the following:
	
	- [ ] All data files are downloaded and directories on google drive created as per instructions: `breakanalysis` folder,
   `Clevel_CBSA_*.xlsx` and `break_dates_cbsaxxxxx.csv` files.
   	- [ ] MSA codes and, names are substituted in the code appropriately as per instructions. 
   	- [ ] File paths and file names are modified as needed.

- [ ] Run the program.

- [ ] Track combined results using this table:

| **Result Name** | **Source File(s)** | **Description** |
| :-- | :-- | :-- |
| Trends for each CBSA/MSA | `plots` directory  | OLS results for each break segment |
| Break Tests| `break_tests_cbsaxxxxx.csv` | Different Break Test results from MATLAB |
| Regression Outputs | `regression_output_industry_est_cbsaxxxxx.xlsx` | Overall regression results from break analysis |

---

## **Final Notes**

- Ensure all file paths are updated throughout the workflow.
- Validate outputs at each stage before proceeding to the next step.
- Save intermediate files for reproducibility.


### Adding Images or Charts

- To include visualizations (e.g., charts of results), use Google Docs' "Insert > Image" feature or paste directly from your local system.

---



