## FunctionDef generate_data_folder_from_qlib
**generate_data_folder_from_qlib**: This function automates the generation of a data folder by utilizing Qlib's backtesting capabilities. It prepares a template directory, runs a specified script within a Docker environment to generate necessary files, and then copies these files into designated output directories for regular and debug data.

parameters:
· No explicit parameters are defined in this function signature; however, it relies on external configurations such as `FACTOR_COSTEER_SETTINGS` which should contain paths for the data folders (`data_folder` and `data_folder_debug`).

Code Description: The function starts by defining a path to a template directory that contains scripts or templates needed for generating factor data. It then initializes an instance of `QTDockerEnv`, which is assumed to be a class responsible for setting up and managing a Docker environment specifically tailored for Qlib operations.

The `prepare` method of the `qtde` object is called to set up the necessary environment within the Docker container. Following this, the function executes a backtest using the `run` method of the `qtde` object. The execution command points to a Python script (`generate.py`) located in the template directory.

After running the backtest, the function asserts that two specific files (`daily_pv_all.h5` and `daily_pv_debug.h5`) have been generated within the template directory as expected results of the backtest. If these files do not exist, an assertion error is raised indicating which file failed to generate.

The function then proceeds to create directories for storing the generated data if they do not already exist. It uses paths specified in `FACTOR_COSTEER_SETTINGS.data_folder` and `FACTOR_COSTEER_SETTINGS.data_folder_debug`. Files from the template directory (`daily_pv_all.h5`, `daily_pv_debug.h5`, and `README.md`) are copied into these directories, with some files being renamed to fit the target directory's naming conventions (e.g., `daily_pv_all.h5` becomes `daily_pv.h5` in the regular data folder).

Note: This function is crucial for preparing the necessary data folders required by other parts of the application. It ensures that all needed data files are generated and placed in the correct locations, facilitating subsequent operations such as retrieving data folder information or performing further analysis. Developers should ensure that `FACTOR_COSTEER_SETTINGS` is properly configured with valid paths before calling this function to avoid errors related to file handling or directory creation.
## FunctionDef get_file_desc(p, variable_list)
**get_file_desc**: This function generates a description of a file based on its type, currently supporting HDF5 (.h5) and Markdown (.md) files.

parameters:
· p: The path to the file for which the description is being generated. It should be an instance of `Path`.
· variable_list: An optional list of variables (columns in case of HDF5 files) that are of interest. This parameter defaults to an empty list if not provided.

Code Description: The function starts by ensuring the input path `p` is a `Path` object. It then defines a template using Jinja2's Environment and StrictUndefined, which will be used to format the file description. Depending on the file extension, it processes the file differently:
- For HDF5 files (.h5), it reads the file into a pandas DataFrame and gathers information about its structure, including index details and column types. If `variable_list` is provided, it filters the columns based on this list. It also checks for a specific column "REPORT_PERIOD" to provide an additional snapshot of the data.
- For Markdown files (.md), it reads the content directly from the file without any processing.
If the file type is not supported (i.e., neither .h5 nor .md), the function raises a `NotImplementedError`.

Note: This function is designed to be used in conjunction with other utilities that handle data folders and generate descriptions for their contents, such as `get_data_folder_intro`. It assumes that the necessary libraries (`pandas`, `jinja2`) are imported elsewhere in the codebase.

Output Example: For an HDF5 file named "data.h5" containing a DataFrame with columns 'price', 'volume', and 'REPORT_PERIOD', and assuming `variable_list=['price']`, the output might look like:
```
data.h5
```h5 info
Index name: date
Related Data columns: 
('price', float64)
A snapshot of one instrument, from which you can tell the distribution of the data:
                REPORT_PERIOD
date       instrument       
2021-01-01  inst_1          2021Q1
2021-01-02  inst_1          2021Q1
2021-01-03  inst_1          2021Q1
2021-01-04  inst_1          2021Q1
2021-01-05  inst_1          2021Q1
```
## FunctionDef get_data_folder_intro(fname_reg, flags, variable_mapping)
**get_data_folder_intro**: This function retrieves information about the contents of a data folder, specifically designed to prepare a prompting message. It filters files based on a regular expression and can optionally map variables for detailed file descriptions.

parameters:
· fname_reg: A string representing a regular expression used to filter filenames within the data folder.
· flags: Flags that modify the behavior of the regular expression matching process.
· variable_mapping: An optional dictionary mapping file stems (without extensions) to lists of variables of interest, used for generating more detailed file descriptions.

Code Description: The function first checks if both the main and debug data folders exist. If either is missing, it calls `generate_data_folder_from_qlib` to create them. It then iterates over files in the debug data folder, filtering based on the provided regular expression and flags. For each matching file, it generates a description using `get_file_desc`. If variable mappings are provided, these are used to enhance the descriptions of HDF5 files by focusing on specific variables. The descriptions are concatenated into a single string with separators indicating individual file entries.

Note: This function is essential for generating detailed summaries of data folder contents, which can be useful for documentation or user prompts. It relies on external configurations and assumes that `FACTOR_COSTEER_SETTINGS` contains valid paths to the data folders.

Output Example:
```
data.h5
```h5 info
Index name: date
Related Data columns: 
('price', float64), ('volume', int32)
A snapshot of one instrument, from which you can tell the distribution of the data:
                REPORT_PERIOD
date       instrument       
2021-01-01  inst_1          2021Q1
2021-01-02  inst_1          2021Q1
2021-01-03  inst_1          2021Q1
2021-01-04  inst_1          2021Q1
2021-01-05  inst_1          2021Q1
```
----------------- file splitter -------------
README.md
```markdown
# Data Folder README

This folder contains the following files:
- daily_pv.h5: Historical price and volume data.
- README.md: This document.
```
```
