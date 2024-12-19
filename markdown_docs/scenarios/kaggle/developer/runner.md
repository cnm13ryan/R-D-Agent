## ClassDef KGCachedRunner
**KGCachedRunner**: This class extends CachedRunner to provide caching mechanisms specific to Kaggle experiments involving feature engineering and model development. It ensures that experiments can be efficiently rerun by utilizing cached results when possible, thereby saving time and computational resources.

attributes:
· exp: An instance of ASpecificExp representing the experiment configuration and data.
· cached_res: An instance of Experiment containing previously computed results for comparison or reuse.

Code Description: The KGCachedRunner class includes methods to generate a cache key based on the content of feature and model Python scripts within an experiment's workspace, assign cached results to an experiment, and initialize development by executing the experiment in a specified environment. The get_cache_key method concatenates the contents of all .py files in the 'feature' and 'model' directories, sorts them alphabetically by filename, and computes their MD5 hash along with any additional cache key information from the superclass CachedRunner. This ensures that changes to these scripts result in different cache keys, preventing stale results.

The assign_cached_result method copies relevant files (CSVs for results and .py files for features/models) from a cached experiment's workspace to the current experiment's workspace. It also transfers the data description from the cached experiment to the current one, ensuring consistency and reusability of previously computed metadata.

The init_develop method sets up the initial development environment by executing the experiment in a specified run environment (with PYTHONPATH set to "./"). After execution, it updates the experiment object with the result. If a 'sub_submission_score.csv' file exists within the workspace path, it reads this file and populates the sub_results attribute of the experiment with scores indexed by model names.

Note: Usage points include scenarios where feature engineering or model development needs to be repeated but can benefit from caching intermediate results to avoid redundant computations. This class is particularly useful in iterative development processes common in Kaggle competitions.

Output Example: A possible appearance of the code's return value after calling init_develop could be an instance of KGFactorExperiment or KGModelExperiment with its result attribute populated by the execution output, and sub_results containing scores from a 'sub_submission_score.csv' file if available. For example:

{
    "result": {
        "model_performance": 0.85,
        "training_time": "12:34"
    },
    "sub_results": {
        "ModelA": 0.82,
        "ModelB": 0.87
    }
}
### FunctionDef get_cache_key(self, exp)
**get_cache_key**: This function generates a unique cache key based on the contents of Python files located in the 'feature' and 'model' directories within an experiment's workspace, combined with a cache key derived from the experiment itself.

parameters:
· exp: An instance of ASpecificExp representing the specific experiment for which the cache key is being generated. This object contains details about the experiment's workspace and other relevant information.

Code Description: The function begins by initializing an empty list named 'codes'. It then iterates over all Python files in the 'feature' directory, sorting them by filename to ensure consistent ordering. For each file, it reads the text content and appends it to the 'codes' list. This process is repeated for the 'model' directory. After collecting all code snippets, these are joined into a single string with newline characters separating each snippet.

Next, the function calls the inherited method `get_cache_key` from the superclass `CachedRunner`, passing in the experiment object. This method likely generates a cache key based on other aspects of the experiment not directly related to the code files. The result is stored in 'cached_key_from_exp'.

Finally, the function concatenates the string of all collected code snippets with the cache key obtained from the experiment and computes an MD5 hash of this combined string. This MD5 hash serves as a unique identifier for the current state of the feature and model code, along with other relevant experimental parameters.

Note: The generated cache key is used to check if previous results can be reused (cached) based on the current state of the experiment's code and configuration. This helps in avoiding unnecessary computations when the same setup has been executed before.

Output Example: 'd41d8cd98f00b204e9800998ecf8427e' (This is a mock MD5 hash value; actual output will vary based on the content of feature and model files and other experiment parameters.)
***
### FunctionDef assign_cached_result(self, exp, cached_res)
**assign_cached_result**: This function assigns cached results to an experiment object by copying relevant files from a cached result's workspace to the current experiment's workspace. It also updates the data description of the experiment with that of the cached result.

parameters:
· exp: An instance of Experiment representing the current experiment where the cached results will be assigned.
· cached_res: An instance of Experiment containing the cached results that need to be copied and used in the current experiment.

Code Description: The function starts by calling the `assign_cached_result` method from the superclass `CachedRunner`, passing both the current experiment (`exp`) and the cached result (`cached_res`). This likely handles some initial setup or assignment related to caching mechanisms. 

Next, it checks if the workspace path of the cached result exists. If it does, the function proceeds to copy all CSV files found in the root directory of the cached result's workspace into the current experiment's workspace. It then copies all Python files from the "feature" subdirectory of the cached result's workspace into the corresponding "feature" subdirectory of the current experiment's workspace. Similarly, it copies all Python files from the "model" subdirectory of the cached result's workspace into the corresponding "model" subdirectory of the current experiment's workspace.

After copying the necessary files, the function updates the `data_description` attribute of the current experiment's workspace with that of the cached result's workspace. This ensures that any metadata or descriptions related to the data used in the cached experiment are also applied to the current experiment.

Finally, the modified `exp` object, now enriched with the cached results and updated descriptions, is returned.

Note: Usage points include scenarios where previous experiments' results can be reused to avoid redundant computations. This function facilitates such reuse by intelligently copying relevant files and metadata from a previously executed experiment to the current one.

Output Example: Assuming `exp` initially had no CSV or Python files in its workspace and an empty data description, after calling `assign_cached_result`, `exp` would have all the CSV and Python files copied from `cached_res`'s workspace. Its `data_description` attribute would also be updated to match that of `cached_res`. The returned `exp` object would look something like this:

```
Experiment(
    experiment_workspace=ExperimentWorkspace(
        workspace_path=<path_to_exp_workspace>,
        data_description=[('task_info_1', 5), ('task_info_2', 3)],
        # ... other attributes
    ),
    # ... other Experiment attributes
)
``` 

Where `<path_to_exp_workspace>` is the path to the experiment's workspace directory, and `data_description` contains tuples of task information and feature shapes copied from the cached result.
***
### FunctionDef init_develop(self, exp)
**init_develop**: This function initializes the development phase by executing a given experiment within a specified environment. It is primarily used as a benchmark for feature engineering during the initial stages of model development.

parameters:
· exp: An instance of either KGFactorExperiment or KGModelExperiment, representing the experiment to be executed.

Code Description: The function starts by setting up an environment dictionary with the PYTHONPATH pointing to the current directory. This ensures that all Python modules are correctly located relative to the project's root. It then executes the experiment using the `execute` method of the experiment's workspace, passing in the previously defined environment settings. The result of this execution is stored back into the experiment object.

Following the execution, the function checks for the existence of a file named "sub_submission_score.csv" within the experiment's workspace path. If this file exists, it reads the CSV file into a pandas DataFrame and extracts the scores associated with different models. These scores are then converted into a dictionary where the keys are model names and the values are their corresponding scores. This dictionary is stored in the `sub_results` attribute of the experiment object.

Finally, the function returns the modified experiment object, which now includes both the execution result and any sub-results from the submission score file.

Note: Usage points include scenarios where initial feature engineering or model development requires a benchmark to compare against. The function ensures that all necessary results are captured and stored within the experiment object for further analysis or use in subsequent steps of the development process.

Output Example: A possible appearance of the code's return value could be an instance of KGFactorExperiment or KGModelExperiment with updated attributes:
- `result`: An object containing the outcome of the experiment execution.
- `sub_results`: A dictionary mapping model names to their scores, e.g., {'model1': 0.85, 'model2': 0.90}.
***
## ClassDef KGModelRunner
**KGModelRunner**: This class extends KGCachedRunner to provide specific functionalities for developing Kaggle model experiments. It includes a caching mechanism to optimize repeated executions of experiments by reusing previously computed results.

attributes:
· exp: An instance of KGModelExperiment representing the experiment configuration and data.
· cached_res: An instance of Experiment containing previously computed results for comparison or reuse.

Code Description: The KGModelRunner class is designed to handle the development phase of Kaggle model experiments. It includes a method `develop` that manages the execution of an experiment, ensuring that necessary checks are performed before running the experiment and handling caching mechanisms efficiently.

The `develop` method first checks if there are any based experiments associated with the current experiment (`exp`). If the last based experiment does not have a result, it initializes this experiment using the `init_develop` method from KGCachedRunner. This ensures that all necessary setup is done before proceeding.

Next, the method retrieves the first sub-workspace from the experiment's sub-workspace list. It then checks if this sub-workspace contains any model code. If no model code is found, it raises a `ModelEmptyError`. Otherwise, it constructs the filename for the model file based on the model type specified in the target task of the sub-workspace and injects this code into the experiment's workspace.

The method sets up the environment to execute the experiment by defining an environment variable PYTHONPATH. It then executes the experiment within this environment using the `execute` method of the experiment's workspace. If no result is returned from the execution, it raises a `CoderError`.

After successfully executing the experiment and obtaining a result, the method updates the experiment object with this result. It also checks for the existence of a 'sub_submission_score.csv' file in the workspace path. If found, it reads this file to extract scores indexed by model names and stores these scores in the `sub_results` attribute of the experiment.

Note: This class is particularly useful in iterative development processes common in Kaggle competitions where experiments need to be run multiple times with different configurations or models. The caching mechanism helps in saving time and computational resources by reusing results from previous executions when possible.

Output Example: A possible appearance of the code's return value after calling `develop` could be an instance of KGModelExperiment with its result attribute populated by the execution output, and sub_results containing scores from a 'sub_submission_score.csv' file if available. For example:

{
    "result": {
        "model_performance": 0.85,
        "training_time": "12:34"
    },
    "sub_results": {
        "ModelA": 0.82,
        "ModelB": 0.87
    }
}
### FunctionDef develop(self, exp)
**develop**: This function manages the development phase of a knowledge graph model experiment by ensuring that any dependent experiments are properly initialized and then executing the current experiment within a specified environment.

parameters:
· exp: An instance of KGModelExperiment, representing the experiment to be developed.

Code Description: The function begins by checking if there are any based experiments associated with the provided experiment (`exp`). If the last based experiment does not have a result (i.e., `exp.based_experiments[-1].result` is None), it calls `self.init_develop(exp.based_experiments[-1])` to initialize that experiment. This ensures that all prerequisite experiments are completed before proceeding.

Next, the function retrieves the first sub-workspace from `exp.sub_workspace_list`. If this sub-workspace exists and contains a model implementation (`sub_ws.code_dict` is not empty), it proceeds to inject the model code into the experiment's workspace. The model file name is constructed based on the model type specified in the target task of the sub-workspace.

If no model is implemented (i.e., `sub_ws.code_dict` is empty or there is no sub-workspace), a `ModelEmptyError` is raised to indicate that no model code is available for execution.

The function then sets up an environment dictionary with the PYTHONPATH pointing to the current directory. This ensures that all Python modules are correctly located relative to the project's root. The experiment is executed using the `execute` method of the experiment's workspace, passing in the defined environment settings. The result of this execution is stored back into the experiment object.

After executing the experiment, the function checks for the existence of a file named "sub_submission_score.csv" within the experiment's workspace path. If this file exists, it reads the CSV file into a pandas DataFrame and extracts the scores associated with different models. These scores are then converted into a dictionary where the keys are model names and the values are their corresponding scores. This dictionary is stored in the `sub_results` attribute of the experiment object.

If no result is returned from the experiment workspace, a `CoderError` is raised to indicate that the execution did not produce any output.

Finally, the function returns the modified experiment object, which now includes both the execution result and any sub-results from the submission score file.

Note: This function is crucial for ensuring that all dependent experiments are completed before proceeding with the current one. It also handles the injection of model code into the workspace and the collection of results, making it a central part of the development process in scenarios involving knowledge graph models.

Output Example: A possible appearance of the code's return value could be an instance of KGModelExperiment with updated attributes:
- `result`: An object containing the outcome of the experiment execution.
- `sub_results`: A dictionary mapping model names to their scores, e.g., {'model1': 0.85, 'model2': 0.90}.
***
## ClassDef KGFactorRunner
**KGFactorRunner**: This class extends KGCachedRunner to provide specific functionality for developing Kaggle experiments involving feature engineering. It manages the execution of sub-workspaces, injects new features into the experiment workspace, and handles caching mechanisms to optimize the development process.

attributes:
· exp: An instance of KGFactorExperiment representing the experiment configuration and data.
· cached_res: An instance of Experiment containing previously computed results for comparison or reuse.

Code Description: The KGFactorRunner class includes a method named `develop` that processes an experiment by iterating through its sub-workspaces. For each sub-workspace, it checks if there is any code implemented. If so, it executes the sub-workspace and retrieves the resulting DataFrame. If the execution yields a valid DataFrame, it increments the count of implemented factors, injects the factor code into the main experiment workspace with a unique filename, appends data description information, and updates the feature file count.

The method also handles cases where no factors are implemented by raising a `FactorEmptyError`. It initializes development for the base experiment if its result is None. The execution environment is set with PYTHONPATH pointing to the current directory. After executing the main experiment workspace, it checks for the presence of 'sub_submission_score.csv' and updates the sub_results attribute of the experiment with scores indexed by model names.

Note: This class is particularly useful in scenarios where feature engineering needs to be iteratively developed and tested within a structured environment that supports caching to avoid redundant computations. It ensures efficient development cycles by leveraging previously computed results when possible.

Output Example: A possible appearance of the code's return value after calling `develop` could be an instance of KGFactorExperiment with its result attribute populated by the execution output, sub_results containing scores from a 'sub_submission_score.csv' file if available, and updated feature files in the workspace. For example:

{
    "result": {
        "model_performance": 0.85,
        "training_time": "12:34"
    },
    "sub_results": {
        "ModelA": 0.82,
        "ModelB": 0.87
    }
}
### FunctionDef develop(self, exp)
**develop**: This function orchestrates the development phase of an experiment by executing sub-workspaces within a given experiment, capturing their results, and integrating them into the main experiment workspace. It ensures that each implemented factor is properly recorded and evaluated.

parameters:
· exp: An instance of KGFactorExperiment, representing the experiment to be developed.

Code Description: The function begins by counting the existing feature files in the experiment's workspace directory. It then iterates over each sub-workspace within the experiment. For each sub-workspace that contains code (i.e., `code_dict` is not empty), it executes the sub-workspace and captures its output DataFrame. If the execution does not produce a DataFrame, the function skips to the next sub-workspace.

For each valid sub-workspace, the function increments the count of implemented factors, creates a new feature file name based on the current count, and injects the sub-workspace's code into the experiment workspace under this new file name. It also appends information about the target task and the shape of the output DataFrame to the data description of the experiment workspace.

If no factors are implemented (i.e., `implemented_factor_count` remains zero), the function raises a FactorEmptyError, indicating that there are no factors to process.

The function then checks if the last based experiment in the experiment's list has a result. If not, it initializes this experiment using the `init_develop` method, which executes the experiment and updates its results and sub-results as described in the documentation for `init_develop`.

An environment dictionary is set up with the PYTHONPATH pointing to the current directory to ensure correct module resolution during execution. The function then executes the entire experiment workspace within this environment.

If the execution does not return a result, a CoderError is raised, indicating that no result was obtained from the experiment workspace.

The function assigns the execution result to the `result` attribute of the experiment object. It also checks for the existence of a "sub_submission_score.csv" file in the experiment's workspace path. If this file exists, it reads the scores from the CSV and stores them in the `sub_results` attribute of the experiment as a dictionary mapping model names to their scores.

Finally, the function returns the updated experiment object, which now includes all new features, results, and sub-results.

Note: This function is crucial for integrating multiple feature engineering steps into a single experiment workflow. It ensures that each step's output is properly captured and stored, allowing for comprehensive analysis and comparison of different factors and models.

Output Example: A possible appearance of the code's return value could be an instance of KGFactorExperiment with updated attributes:
- `result`: An object containing the outcome of the experiment execution.
- `sub_results`: A dictionary mapping model names to their scores, e.g., {'model1': 0.85, 'model2': 0.90}.
- `data_description`: A list of tuples containing task information and feature shapes, e.g., [('task1', 10), ('task2', 15)].
***
