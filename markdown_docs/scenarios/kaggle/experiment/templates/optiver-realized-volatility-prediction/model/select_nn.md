## FunctionDef select(X)
**select**: This function selects relevant features from a given DataFrame to be used in fit & predict functions within a machine learning pipeline. Currently, it assumes all features are relevant but includes logic for handling multi-level column names by flattening them.

parameters:
· X: A pandas DataFrame containing the dataset with potential multi-level columns.

Code Description: The function begins by checking if the DataFrame `X` has single-level column names using `X.columns.nlevels`. If true, it returns the DataFrame as is. Otherwise, it proceeds to flatten any multi-level column names into a single level by joining each tuple of column labels into a string separated by underscores. This flattened list of column names is then assigned back to `X.columns`, and the modified DataFrame is returned.

Note: The function's current implementation does not perform actual feature selection but rather prepares the DataFrame for further processing by ensuring that all column names are single-level strings, which can be crucial for compatibility with certain machine learning libraries or algorithms that do not support multi-level indexing.

Output Example: Given a DataFrame `X` with multi-level columns like:
```
         time_id  stock_id
0              5        18
1             23        47
```
The function will return:
```
   time_id_stock_id
0                5_18
1               23_47
```
