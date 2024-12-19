## ClassDef QlibFactorRunner
**QlibFactorRunner**: This class is designed to manage and process factor data within a quantitative finance context using Qlib, an open-source library for financial time series modeling. It extends the functionality of `CachedRunner` specifically tailored for experiments involving factors (predictive variables) used in financial models.

attributes:
· exp: An instance of `QlibFactorExperiment`, which encapsulates all necessary configurations and data related to a factor experiment.
· SOTA_feature: A DataFrame representing the state-of-the-art (SOTA) features, which are the current best-performing factors.
· new_feature: A DataFrame containing newly developed or candidate factors that need evaluation against existing ones.

Code Description: The `QlibFactorRunner` class includes methods to calculate information coefficients between different sets of factors, deduplicate new factors based on their correlation with SOTA factors, and develop experiments by processing factor data. The `calculate_information_coefficient` method computes the correlation (information coefficient) between each pair of columns from two DataFrames, one representing existing features and another representing new features. This helps in assessing how much information is shared between old and new factors.

The `deduplicate_new_factors` method uses the calculated information coefficients to filter out new factors that are highly correlated with existing ones, reducing redundancy. It concatenates SOTA and new feature DataFrames, calculates mean ICs across different timestamps, and removes columns from the new features DataFrame where the maximum IC exceeds a threshold (0.99 in this case).

The `develop` method orchestrates the entire process of factor development. It first ensures that all base experiments are processed recursively if they haven't been already. Then, it processes both SOTA and new factors using the `process_factor_data` method. After deduplication, it combines these factors into a single DataFrame, sorts them, removes any duplicate columns, and nests them under a 'feature' label in a MultiIndex structure. This combined DataFrame is then saved to disk for further use.

The `process_factor_data` method collects factor data from one or more experiments by executing each sub-implementation within the experiment. It filters out any factors that do not meet certain criteria (e.g., having a time difference of exactly one minute between consecutive timestamps). If valid factor data is found, it combines these into a single DataFrame; otherwise, it raises an error indicating no valid factor data was found.

Note: The `QlibFactorRunner` class assumes the existence of specific configurations and methods such as `multiprocessing_wrapper`, `RD_AGENT_SETTINGS.multi_proc_n`, and `CachedRunner`. It also relies on external libraries like pandas for data manipulation and pickle for serialization. Developers should ensure these dependencies are correctly set up in their environment.

Output Example: The output of the `develop` method is an instance of `QlibFactorExperiment` with its `result` attribute populated by backtest results obtained from executing the experiment within a Docker container. The combined factors DataFrame, which includes both SOTA and deduplicated new factors, is saved to a file named "combined_factors_df.pkl" in the experiment's workspace directory.

Example:
```
QlibFactorExperiment(
    based_experiments=[...],
    sub_workspace_list=[...],
    experiment_workspace=ExperimentWorkspace(workspace_path=Path(...)),
    result={
        'backtest_results': {
            'sharpe_ratio': 1.234,
            'annual_return': 0.15,
            ...
        },
        ...
    }
)
```
### FunctionDef calculate_information_coefficient(self, concat_feature, SOTA_feature_column_size, new_feature_columns_size)
**calculate_information_coefficient**: This function calculates the information coefficient (IC) between each column of a set of standard-of-the-art (SOTA) features and a set of new features within a concatenated DataFrame.

parameters:
· concat_feature: A pandas DataFrame containing both SOTA features and new features concatenated along columns.
· SOTA_feature_column_size: An integer representing the number of columns in the SOTA feature subset of concat_feature.
· new_feature_columns_size: An integer representing the number of columns in the new feature subset of concat_feature.

Code Description: The function initializes a pandas Series, res, to store the calculated information coefficients. It iterates over each column in the SOTA features and each column in the new features. For every pair of columns (one from SOTA features and one from new features), it calculates the Pearson correlation coefficient using the corr() method on the respective DataFrame slices. This coefficient is then stored in the res Series at an index calculated based on the current positions of col1 and col2 within their respective subsets. The function finally returns this Series containing all the information coefficients.

Note: The function assumes that concat_feature has been properly preprocessed to include both SOTA features and new features, with no missing values affecting the correlation calculation. It is designed to be used in scenarios where feature selection or deduplication based on similarity (measured by IC) is required.

Output Example: If SOTA_feature_column_size is 3 and new_feature_columns_size is 2, the output Series might look like this:
```
0    0.85
1    -0.47
2    0.63
3    0.91
dtype: float64
```
Each value in the Series represents the information coefficient between a pair of SOTA and new features, with indices indicating which pairs were compared.
***
### FunctionDef deduplicate_new_factors(self, SOTA_feature, new_feature)
**deduplicate_new_factors**: This function aims to remove new features from a DataFrame if they have a high information coefficient (IC) with any of the standard-of-the-art (SOTA) features, indicating that the new features may not add unique predictive power.

parameters:
· SOTA_feature: A pandas DataFrame containing the existing set of SOTA features.
· new_feature: A pandas DataFrame containing the new features to be evaluated and potentially deduplicated.

Code Description: The function begins by concatenating the SOTA features and new features along columns into a single DataFrame named `concat_feature`. It then calculates the information coefficient (IC) between each column in the SOTA feature set and each column in the new feature set. This calculation is performed on a per-datetime basis using the `calculate_information_coefficient` method, which computes Pearson correlation coefficients for all pairwise combinations of SOTA and new features. The IC values are averaged across all datetimes to obtain an overall measure of similarity between each pair of features.

The resulting Series, `IC_max`, contains the maximum IC value for each new feature when compared against any SOTA feature. If this maximum IC exceeds a predefined threshold (0.99 in this case), indicating that the new feature is highly correlated with at least one SOTA feature, it is considered redundant and removed from the set of new features.

The function returns a DataFrame containing only those new features whose maximum IC with any SOTA feature is below the specified threshold, thus ensuring that the returned set of new features does not contain duplicates or highly similar features already represented by the SOTA features.

Note: The function assumes that both `SOTA_feature` and `new_feature` DataFrames are preprocessed and do not contain missing values. It relies on the `calculate_information_coefficient` method to compute ICs, which in turn uses Pearson correlation coefficients as a measure of similarity between feature pairs.

Output Example: Suppose we have two DataFrames:
- SOTA_feature with columns ['SOTA1', 'SOTA2']
- new_feature with columns ['New1', 'New2']

After processing, if the maximum IC values for 'New1' and 'New2' when compared against 'SOTA1' and 'SOTA2' are [0.95, 0.87] respectively, then the function will return a DataFrame containing only 'New2', as its maximum IC (0.87) is below the threshold of 0.99. The returned DataFrame might look like this:
```
            New2
datetime        
2021-01-01   0.5
2021-01-02   -0.3
2021-01-03   0.7
```
***
### FunctionDef develop(self, exp)
**develop**: This function generates an experiment by processing and combining factor data from a given QlibFactorExperiment object. It handles cases where the experiment is based on previous experiments, ensuring that the most up-to-date and relevant factor data is used for backtesting within Docker.

parameters:
· exp: A QlibFactorExperiment object containing the experimental setup and factor data to be processed.

Code Description: The function starts by checking if there are any base experiments associated with the current experiment (`exp.based_experiments`). If the last base experiment does not have a result, it recursively calls `develop` on that experiment to ensure all dependencies are resolved. 

Next, if there are base experiments, the function identifies the state-of-the-art (SOTA) factor data by processing the base experiments' data using `process_factor_data`. This step is skipped if there is only one base experiment.

The function then processes the new factors from the current experiment (`exp`) using `process_factor_data` again. If no valid new factor data is found, it raises a `FactorEmptyError`.

If SOTA factor data exists and is not empty, the function deduplicates the new factors against the SOTA factors using `deduplicate_new_factors`. This ensures that only unique and non-redundant features are considered for further analysis. If the deduplication process results in no valid new factors, a `FactorEmptyError` is raised.

The combined factor data (SOTA factors plus new factors) is then sorted by index and any duplicate columns are removed to maintain data integrity. The resulting DataFrame's columns are renamed to be nested under 'feature' for clarity.

The combined factors are saved to the experiment's workspace in a file named `combined_factors_df.pkl` using pickle serialization.

Finally, the function executes the backtest within Docker using the appropriate configuration file (`conf.yaml` for no base experiments or `conf_combined.yaml` for experiments with base experiments). The result of this execution is stored in the experiment object and returned.

Note: This function is essential for preparing factor data for backtesting by ensuring that all necessary preprocessing steps are performed, including deduplication and combination of factors from multiple sources. It also handles recursive development of base experiments to ensure all dependencies are met before proceeding with the current experiment.

Output Example: A possible return value of this function could be a QlibFactorExperiment object with its `result` attribute populated with backtest results. The combined factor data would have been saved in the workspace as `combined_factors_df.pkl`. Here is a simplified example of what the `result` might look like:

```
{
    "backtest_metrics": {
        "sharpe_ratio": 1.23,
        "annual_return": 0.15,
        "max_drawdown": -0.08
    },
    "factor_data": {
        "SOTA1": [0.5, 0.4, 0.6],
        "New2": [-0.2, -0.1, -0.3]
    }
}
```

This example illustrates a backtest result with key performance metrics and the combined factor data used in the backtest.
***
### FunctionDef process_factor_data(self, exp_or_list)
**process_factor_data**: This function processes and combines factor data from one or multiple experiment implementations into a single DataFrame without NaN values.

parameters:
· exp_or_list: A single QlibFactorExperiment object or a list of QlibFactorExperiment objects containing factor data to be processed.

Code Description: The function first checks if the input is a single QlibFactorExperiment and converts it into a list if necessary. It then initializes an empty list, factor_dfs, to store DataFrames from each experiment's sub-implementations. For each experiment in the provided list, it uses multiprocessing_wrapper to execute all sub-implementations concurrently, collecting their outputs as tuples of messages and DataFrames. Each DataFrame is checked for validity (not None and containing a 'datetime' index). If the DataFrame passes these checks and has a consistent time frequency of one minute between timestamps, it is added to factor_dfs. After processing all experiments, if there are any valid DataFrames in factor_dfs, they are concatenated along columns into a single DataFrame, which is then returned. If no valid DataFrames are found, the function raises a FactorEmptyError.

Note: This function is crucial for aggregating and validating factor data from multiple experiments before further processing or analysis. It ensures that only high-quality, consistent data is used in subsequent steps.

Output Example: A possible return value of this function could be a DataFrame with columns representing different factors and rows indexed by timestamps. Here's an example structure:

| datetime             | Factor1 | Factor2 | Factor3 |
|----------------------|---------|---------|---------|
| 2023-01-01 09:30:00  | 0.5     | -0.2    | 0.8     |
| 2023-01-01 09:31:00  | 0.4     | -0.1    | 0.7     |
| 2023-01-01 09:32:00  | 0.6     | -0.3    | 0.9     |

Each entry in the DataFrame represents a factor value at a specific timestamp, with all NaN values removed to ensure data integrity.
***
