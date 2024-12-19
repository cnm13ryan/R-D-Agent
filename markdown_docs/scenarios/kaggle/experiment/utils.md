## FunctionDef python_files_to_notebook(competition, py_dir)
**python_files_to_notebook**: This function consolidates multiple Python scripts into a single Jupyter Notebook file (.ipynb) and a corresponding Python script file (.py). It is designed to organize code from a Kaggle competition directory, specifically preprocessing, feature engineering, model training, and selection scripts.

**parameters**:
· competition: A string representing the name of the Kaggle competition. This parameter is used to dynamically adjust paths within the pre-processing script.
· py_dir: A string or Path object pointing to the directory containing the Python files to be merged. The directory should have a specific structure with subdirectories for feature engineering, model training, and selection scripts.

**Code Description**: Detailed analysis and description.
The function begins by converting the `py_dir` parameter into a `Path` object for easier file manipulation. It then defines the path where the resulting Jupyter Notebook will be saved (`merged.ipynb`). 

Firstly, it reads the content of the pre-processing script located at `fea_share_preprocess.py`. This script is modified to include the competition name in paths that reference Kaggle input directories.

Next, the function gathers all feature engineering scripts from the `feature` subdirectory. Each script's content is read and stored in a dictionary (`fea_pys`) with keys derived from the file names (stem). The function also ensures each script ends with a call to its main class or function.

Model training scripts from the `model` directory are then processed similarly, but additional modifications are made. Specifically, functions within these scripts are converted into methods of a new class named after the script's stem. This involves adding an indentation level to all lines following function definitions and inserting a class definition line before each method.

Selection scripts, also located in the `model` directory, are read and stored in another dictionary (`select_pys`). These scripts undergo minor modifications to rename their main functions according to the file name.

The training script (`train.py`) is then processed. It undergoes several replacements:
- The import statement for the pre-processing script is removed.
- A line setting `DIRNAME` is deleted as it's no longer necessary in a single-file context.
- Loops and imports related to dynamically loading feature engineering and model classes are replaced with static references using lists of class names generated earlier.

All processed scripts are then added as code cells to a new Jupyter Notebook object (`nb`). Additionally, the content of all scripts is concatenated into a single string (`all_py`), which will be saved as a Python script file alongside the notebook.

Finally, both the Jupyter Notebook and the combined Python script are written to disk at the specified `save_path`.

**Note**: Usage points.
This function is particularly useful for Kaggle competition participants who need to consolidate their code into a single notebook or script for submission or sharing. It assumes a specific directory structure and naming conventions for scripts, so users should ensure their project adheres to these expectations. The function also makes several assumptions about the content of the scripts (e.g., presence of certain functions or classes), which may require adjustments if the codebase differs significantly from typical Kaggle competition setups.
