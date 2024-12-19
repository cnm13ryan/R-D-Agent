## FunctionDef classify_report_from_dict(report_dict, vote_time, substrings)
**classify_report_from_dict**: This function processes a dictionary of PDF reports to classify each report as either relevant (1) or irrelevant (0) based on its content. The classification is performed by sending the content of each report to an API that returns a classification result, which is then aggregated through voting.

parameters:
· report_dict: A dictionary where each key is the file path of a PDF report and the value is the content of the report as a string.
· vote_time: An integer specifying how many times the classification process should be repeated for each report to ensure consistency. The default value is 1.
· substrings: A tuple of strings that can be used to pre-filter documents based on the presence of specific keywords. This feature is currently commented out in the code.

Code Description: The function iterates over each entry in the `report_dict`. It checks if the file path ends with ".pdf" and whether the content is a string. If the content is not a string, it logs a warning and classifies the report as irrelevant (0). For each valid PDF report, the function trims the content to fit within a token limit defined by `LLM_SETTINGS.chat_token_limit`. It then sends the content to an API for classification up to `vote_time` times. The results from these votes are aggregated, and the majority vote determines the final classification of the report (0 or 1). This result is stored in a dictionary with the file path as the key.

Note: Usage points include scenarios where reports need to be filtered based on their content relevance for further processing, such as extracting factors from relevant reports. The function leverages an external API for classification and handles potential errors in API responses gracefully by defaulting to class 0 if parsing fails.

Output Example: A possible return value of the function could be:
```
{
    "path/to/report1.pdf": {"class": 1},
    "path/to/report2.pdf": {"class": 0},
    "path/to/report3.pdf": {"class": 1}
}
```
This output indicates that `report1.pdf` and `report3.pdf` are classified as relevant, while `report2.pdf` is classified as irrelevant.
## FunctionDef __extract_factors_name_and_desc_from_content(content)
**__extract_factors_name_and_desc_from_content**: This function extracts factor names and their corresponding descriptions from a given text content using an API-based chat session mechanism.

parameters:
· content: A string containing the document or report text from which factors and their descriptions need to be extracted.

Code Description: The function initializes a chat session with a predefined system prompt designed for extracting factors. It iteratively sends user prompts to this session, expecting JSON-formatted responses that contain factor names as keys and their descriptions as values. Each iteration processes the response to update the dictionary of extracted factors until no more factors are found in the response or after a maximum of 10 iterations. The function returns a dictionary where each key is a factor name and its value is another dictionary containing the description of that factor.

Note: This function relies on an external API for processing text content, which must be properly configured with the necessary prompts and capabilities to extract factors accurately. It also assumes that the API response will be in JSON format with a specific structure containing a "factors" key.

Output Example: 
{
    'FactorA': {'description': 'This factor measures the performance of asset A.'},
    'FactorB': {'description': 'This factor assesses the volatility of asset B.'}
}
## FunctionDef __extract_factors_formulation_from_content(content, factor_dict)
**__extract_factors_formulation_from_content**: This function extracts formulations and descriptions of financial factors from a given content string based on a predefined dictionary of factor names and their descriptions.

parameters:
· content: A string containing the text from which factor formulations need to be extracted.
· factor_dict: A dictionary where keys are factor names (strings) and values are their corresponding descriptions (strings).

Code Description: The function starts by converting the input dictionary into a pandas DataFrame for easier manipulation. It then prepares a system prompt and a user prompt using predefined templates, incorporating the content and the string representation of the DataFrame.

A chat session is initiated with these prompts to interact with an API that processes natural language inputs and generates structured outputs in JSON format. The function iteratively sends requests to this API until it receives formulations for all factors listed in the input dictionary or after a maximum of 10 attempts.

During each iteration, the response from the API is parsed as a JSON object. If any factor names found in the response match those in the input dictionary, their formulations and descriptions are stored in a new dictionary named `factor_to_formulation`. If not all factors have been accounted for after an attempt, the function updates the user prompt to include only the remaining factors and repeats the process.

Note: This function is designed to handle cases where some factor formulations might be missing from the initial response by repeatedly querying the API with updated prompts until all factors are extracted or a limit of 10 attempts is reached. It assumes that the API can understand and respond appropriately to the provided prompts in JSON mode.

Output Example: A possible return value of this function could look like:
{
    "factor1": {"formulation": "Earnings / Price", "variables": ["Earnings", "Price"]},
    "factor2": {"formulation": "Debt / Equity", "variables": ["Debt", "Equity"]}
}
This output indicates that the formulations for two factors, "factor1" and "factor2," have been successfully extracted from the content. Each factor's entry includes its formulation as a string and a list of variables involved in the calculation.
## FunctionDef __extract_factor_and_formulation_from_one_report(content)
**__extract_factor_and_formulation_from_one_report**: This function processes a given text content to extract financial factors along with their descriptions and formulations. It leverages two helper functions to first identify factor names and descriptions, and then to derive the mathematical formulations of these factors.

parameters:
· content: A string containing the document or report text from which factors, their descriptions, and formulations need to be extracted.

Code Description: The function begins by initializing an empty dictionary `final_factor_dict_to_one_report` to store the final results. It then calls `__extract_factors_name_and_desc_from_content`, passing in the content parameter, to obtain a dictionary of factor names mapped to their descriptions. If this dictionary is not empty, it proceeds to call `__extract_factors_formulation_from_content`, which extracts formulations and associated variables for each identified factor.

The function iterates over each factor name found in the initial extraction step. For each factor, it checks if the factor has a corresponding formulation and list of variables in the results from `__extract_factors_formulation_from_content`. If any of these components are missing, the loop continues to the next factor.

If all necessary information is available for a factor, the function prepares the formulation string by escaping underscores within the factor name and its associated variables. This step ensures that underscores do not interfere with LaTeX or similar formatting systems when the formulations are used elsewhere.

The final dictionary `final_factor_dict_to_one_report` is populated with each factor's description, corrected formulation, and list of variables. After processing all factors, this dictionary is returned as the output.

Note: The function assumes that both helper functions (`__extract_factors_name_and_desc_from_content` and `__extract_factors_formulation_from_content`) are correctly implemented and configured to interact with an external API for text processing. It also relies on the proper formatting of responses from these APIs, which should be in JSON format with specific structures.

Output Example: A possible return value of this function could look like:
{
    "FactorA": {
        "description": "This factor measures the performance of asset A.",
        "formulation": "Earnings\_A / Price\_A",
        "variables": ["Earnings_A", "Price_A"]
    },
    "FactorB": {
        "description": "This factor assesses the volatility of asset B.",
        "formulation": "StdDev\_Returns\_B",
        "variables": ["Returns_B"]
    }
}
This output indicates that two factors, "FactorA" and "FactorB," have been successfully extracted from the content. Each factor's entry includes its description as a string, its formulation with escaped underscores for proper formatting, and a list of variables involved in the calculation.
## FunctionDef extract_factors_from_report_dict(report_dict, useful_no_dict, n_proc)
**extract_factors_from_report_dict**: This function processes a dictionary of reports to extract financial factors based on specified criteria. It filters out relevant reports, extracts factors from each report using multiprocessing for efficiency, and returns a structured dictionary containing these factors.

parameters:
· report_dict: A dictionary where keys are file names or identifiers and values are the text content of the reports.
· useful_no_dict: A dictionary that indicates which reports contain useful information. Each key corresponds to a report in `report_dict`, and its value is another dictionary with a "class" key indicating whether the report is useful (value 1) or not.
· n_proc: An integer specifying the number of processes to use for multiprocessing. The default value is 11.

Code Description: The function starts by initializing an empty dictionary `useful_report_dict` to store reports that are deemed useful based on the criteria provided in `useful_no_dict`. It iterates through each key-value pair in `useful_no_dict`, checking if the "class" value is 1, which indicates a useful report. If so, it adds the corresponding report from `report_dict` to `useful_report_dict`.

Next, the function creates a list of file names (`file_name_list`) from the keys of `useful_report_dict`. It then initializes another empty dictionary `final_report_factor_dict` to store the final results.

The function uses multiprocessing to efficiently extract factors and their formulations from each useful report. This is achieved by calling `multiprocessing_wrapper`, which takes a list of tuples as input, where each tuple contains a function (`__extract_factor_and_formulation_from_one_report`) and its arguments (the content of one report). The number of processes used for multiprocessing is determined by the `n` parameter passed to `multiprocessing_wrapper`.

After processing all reports, the function maps the results back to their respective file names in `final_report_factor_dict`. It logs a message indicating that factor extraction has been completed for the specified number of reports.

Note: The function relies on the `__extract_factor_and_formulation_from_one_report` function to perform the actual extraction of factors and formulations from each report. This helper function processes the text content, identifies financial factors, and derives their descriptions and mathematical formulations.

Output Example: A possible return value of this function could look like:
{
    "report1.pdf": {
        "FactorA": {
            "description": "This factor measures the performance of asset A.",
            "formulation": "Earnings\_A / Price\_A",
            "variables": ["Earnings_A", "Price_A"]
        },
        "FactorB": {
            "description": "This factor assesses the volatility of asset B.",
            "formulation": "StdDev\_Returns\_B",
            "variables": ["Returns_B"]
        }
    },
    "report2.pdf": {
        "FactorC": {
            "description": "This factor evaluates the liquidity of asset C.",
            "formulation": "Volume\_C / Price\_C",
            "variables": ["Volume_C", "Price_C"]
        }
    }
}
This output indicates that factors have been successfully extracted from two reports. Each report's entry contains a dictionary of factors, where each factor is represented by its description, formulation with escaped underscores for proper formatting, and a list of variables involved in the calculation.
## FunctionDef merge_file_to_factor_dict_to_factor_dict(file_to_factor_dict)
**merge_file_to_factor_dict_to_factor_dict**: This function consolidates factor data from multiple files into a single dictionary while handling potential duplicates by selecting the factor with the longest formulation.

parameters:
· file_to_factor_dict: A nested dictionary where each key is a file name and its value is another dictionary. The inner dictionary contains factors as keys, and their corresponding data (including formulations) as values.

Code Description: Detailed analysis and description.
The function starts by initializing an empty dictionary named `factor_dict`. It then iterates over each file in the provided `file_to_factor_dict` structure. For each file, it further iterates through its associated factors. If a factor is not already present in `factor_dict`, it adds the factor with an empty list as its value using `setdefault()`. The function appends the factor's data from the current file to the corresponding list in `factor_dict`.

After collecting all factors and their data across files, the function initializes another dictionary named `factor_dict_simple_deduplication` for storing the final set of factors. It then iterates over each factor in `factor_dict`. If a factor appears in more than one file (i.e., its associated list has more than one entry), it selects the version with the longest formulation using the `max()` function with a key that measures the length of the "formulation" field. This ensures that, in cases of duplication, the most detailed or comprehensive definition is retained. If a factor appears only once across all files, its data is directly added to `factor_dict_simple_deduplication`.

Note: Usage points.
This function is particularly useful when dealing with multiple sources of factor data that may contain overlapping information. It helps in consolidating and deduplicating the data efficiently.

Output Example: Mock up a possible appearance of the code's return value.
{
    'FactorA': {'name': 'FactorA', 'formulation': 'Detailed formulation for Factor A'},
    'FactorB': {'name': 'FactorB', 'formulation': 'Another detailed formulation for Factor B'},
    'FactorC': {'name': 'FactorC', 'formulation': 'Longest and most comprehensive formulation for Factor C'}
}
## FunctionDef __check_factor_dict_relevance(factor_df_string)
**__check_factor_dict_relevance**: This function evaluates the relevance of factors contained within a string representation of a DataFrame by leveraging an API to determine their significance.

parameters:
· factor_df_string: A string that represents a DataFrame containing factor information, typically generated using the `to_string()` method on a pandas DataFrame. This string is used as input for the API call to assess the relevance of each factor.

Code Description: Detailed analysis and description.
The function starts by invoking an instance of `APIBackend` and utilizing its method `build_messages_and_create_chat_completion`. It sends a system prompt designed for assessing factor relevance (`document_process_prompts["factor_relevance_system"]`) along with the user prompt, which is the string representation of the DataFrame containing factors. The `json_mode=True` parameter indicates that the response from the API should be in JSON format.

The function then parses this JSON response using `json.loads()` and returns it as a dictionary where each key corresponds to a factor name and its value is another dictionary detailing the relevance assessment results for that factor.

Note: Usage points.
This function is typically used within a larger context, such as the `check_factor_relevance` function, which processes batches of factors in parallel using multiprocessing. It plays a crucial role in filtering out irrelevant or less significant factors from a dataset based on automated assessments provided by an external API.

Output Example: Mock up a possible appearance of the code's return value.
{
    "factor1": {"relevance": true, "reason": "High predictive power"},
    "factor2": {"relevance": false, "reason": "No significant impact observed"},
    "factor3": {"relevance": true, "reason": "Consistent performance across datasets"}
}
## FunctionDef check_factor_relevance(factor_dict)
**check_factor_relevance**: This function evaluates the relevance of factors contained within a dictionary by leveraging an external API to determine their significance. It processes the factors in batches, using multiprocessing for efficiency.

parameters:
· factor_dict: A dictionary where each key is a factor name and its value is another dictionary containing information about that factor.

Code Description: The function begins by initializing an empty dictionary `factor_relevance_dict` to store the relevance assessment results of each factor. It then converts the input dictionary into a pandas DataFrame, setting the index names to "factor_name". 

The function enters a loop that continues until all factors have been processed. Within this loop, it divides the DataFrame into smaller chunks (up to 50 rows per chunk) and uses the `multiprocessing_wrapper` function to process these chunks in parallel. Each chunk is converted to a string representation using the `to_string()` method and passed to the `__check_factor_dict_relevance` function, which assesses the relevance of the factors contained within that chunk.

The results from each processed chunk are collected into `result_list`, and for each result, the factor name and its relevance assessment (a dictionary) are added to `factor_relevance_dict`. After processing a batch of factors, the DataFrame is updated to exclude those factors that have already been assessed, ensuring that each factor is only evaluated once.

Once all factors have been processed, the function constructs a new dictionary `filtered_factor_dict` that includes only those factors deemed relevant (i.e., where the "relevance" key in their assessment dictionary is set to True).

Note: This function is particularly useful in scenarios where a large number of factors need to be evaluated for relevance, as it utilizes multiprocessing to speed up the process. It relies on an external API to perform the actual relevance assessments.

Output Example:
{
    "factor1": {"relevance": true, "reason": "High predictive power"},
    "factor2": {"relevance": false, "reason": "No significant impact observed"},
    "factor3": {"relevance": true, "reason": "Consistent performance across datasets"}
}
## FunctionDef __check_factor_dict_viability_simulate_json_mode(factor_df_string)
**__check_factor_dict_viability_simulate_json_mode**: This function checks the viability of a factor dictionary by sending it to an API for evaluation and returns the result as a dictionary.

parameters:
· factor_df_string: A string representation of a DataFrame containing factors, which is prepared for evaluation via an API call.

Code Description: The function takes a string that represents a DataFrame of factors. This string is then used in conjunction with predefined system prompts to create a chat completion request through the `APIBackend` class. The `json_mode=True` parameter indicates that the response from the API should be in JSON format. After receiving the response, it is parsed using `json.loads()` and returned as a dictionary where each key corresponds to a factor name and its associated viability details.

Note: This function is typically used internally within other functions like `check_factor_viability`, which processes larger sets of factors by breaking them into smaller chunks (up to 50 rows at a time) for evaluation. The results are then aggregated to form a comprehensive viability dictionary.

Output Example: 
{
    "factor1": {"viability": true, "reason": "Factor meets all criteria"},
    "factor2": {"viability": false, "reason": "Insufficient historical data"}
}
## FunctionDef check_factor_viability(factor_dict)
**check_factor_viability**: This function evaluates the viability of a set of factors by checking each one through an API call. It processes the input dictionary of factors, breaks it into manageable chunks, and uses multiprocessing to enhance efficiency. The results from these checks are aggregated to determine which factors meet the criteria for viability.

parameters:
· factor_dict: A dictionary where keys are factor names and values are dictionaries containing details about each factor.

Code Description: The function starts by initializing an empty dictionary `factor_viability_dict` to store the viability status of each factor. It then converts the input `factor_dict` into a DataFrame, setting the index to "factor_name". The function enters a loop that continues until all factors have been evaluated. Inside the loop, it divides the DataFrame into chunks (up to 50 rows at a time) and uses the `multiprocessing_wrapper` function to call `__check_factor_dict_viability_simulate_json_mode` for each chunk. This helper function sends the factor data to an API for evaluation and returns the results in JSON format. The viability information is then extracted from these results and stored in `factor_viability_dict`. After processing a batch of factors, the DataFrame is updated to exclude those that have already been evaluated. Once all factors are processed, the function creates a new dictionary `filtered_factor_dict` containing only the factors deemed viable according to the API's evaluation.

Note: This function is typically used as part of a larger process where factors are extracted from documents and then filtered based on their viability before further analysis or experimentation.

Output Example:
{
    "factor_viability_dict": {
        "factor1": {"viability": true, "reason": "Factor meets all criteria"},
        "factor2": {"viability": false, "reason": "Insufficient historical data"}
    },
    "filtered_factor_dict": {
        "factor1": {"details": "specific details about factor1"}
    }
}
## FunctionDef __check_factor_duplication_simulate_json_mode(factor_df)
**__check_factor_duplication_simulate_json_mode**: This function checks for duplicated factors within a given DataFrame by simulating JSON mode interactions with an API backend. It processes the DataFrame to identify groups of potentially duplicate factors based on their string representations.

parameters:
· factor_df: A pandas DataFrame containing factors, where each row represents a different factor and its associated attributes.

Code Description: The function starts by converting the input DataFrame into a string format which serves as the initial user prompt for API interactions. It then divides this DataFrame into smaller chunks if necessary to ensure that the token limit of the API backend is not exceeded. This division process continues recursively until all resulting DataFrames are small enough to be processed within the token limit.

Each chunk is then used to build a chat session with the API backend, using predefined system prompts designed for detecting factor duplication. The function sends these chunks as user prompts in JSON mode and expects a response containing potential duplicated groups of factors. This process is repeated up to 10 times per chunk to ensure comprehensive detection of duplicates.

The responses from the API are parsed into dictionaries, and any identified duplicate groups are added to a list. If an empty dictionary is returned by the API, indicating no more duplicates are found, the function stops processing that particular chunk. This process continues until all chunks have been processed, and the final list of duplicated factor groups is returned.

Note: The function relies on external settings such as `LLM_SETTINGS.chat_token_limit` for managing token usage and `document_process_prompts["factor_duplicate_system"]` for providing system prompts to the API backend. It also assumes that the API backend has methods like `build_messages_and_calculate_token`, `build_chat_session`, and `build_chat_completion`.

Output Example: A possible return value of this function could be a list of lists, where each inner list contains the names of factors identified as duplicates:
```
[['factor1', 'factor2'], ['factor3', 'factor4', 'factor5']]
``` 
This indicates that 'factor1' and 'factor2' are considered duplicates, as well as 'factor3', 'factor4', and 'factor5'.
## FunctionDef __kmeans_embeddings(embeddings, k)
**__kmeans_embeddings**: This function performs clustering on a set of embeddings using the K-means algorithm with cosine similarity as the distance metric. It returns a list of lists, where each inner list contains indices of data points that belong to the same cluster.

parameters:
· embeddings: A numpy ndarray containing the embeddings to be clustered.
· k: An integer representing the number of clusters to form (default is 20).

Code Description: The function starts by normalizing the input embeddings. It then initializes a KMeans object with specified parameters such as the number of clusters, initialization method, maximum iterations, and random state for reproducibility.

A custom function `find_closest_cluster_cosine_similarity` is defined to find the closest cluster center for each data point using cosine similarity instead of Euclidean distance, which is the default in KMeans. This function computes the cosine similarity between the data points and the centroids and returns the index of the most similar centroid for each data point.

The initial centroids are randomly selected from the normalized embeddings. The algorithm iteratively assigns each data point to its closest cluster center based on cosine similarity and then recalculates the centroids as the mean of all data points in each cluster, normalizing these new centroids. This process continues until the centroids no longer change significantly or the maximum number of iterations is reached.

After convergence, the function uses `find_closest_cluster_cosine_similarity` to assign all data points to their final clusters and organizes them into a dictionary mapping cluster indices to lists of data point indices. The function returns these lists sorted by descending order of size, indicating which clusters contain the most data points.

Note: This function is particularly useful in scenarios where embeddings need to be grouped based on semantic similarity rather than Euclidean distance, such as clustering similar text documents or factors in financial analysis.

Output Example: [[10, 23, 45], [7, 9, 12, 34], [1, 5, 8, 16, 22]] - This output indicates that there are three clusters. The first cluster contains data points with indices 10, 23, and 45; the second cluster includes data points at indices 7, 9, 12, and 34; and the third cluster consists of data points at indices 1, 5, 8, 16, and 22. The clusters are ordered by size, with the largest cluster first.
### FunctionDef find_closest_cluster_cosine_similarity(data, centroids)
**find_closest_cluster_cosine_similarity**: This function computes the cosine similarity between a set of data points and cluster centroids to determine which centroid each data point is closest to.

parameters:
· data: A two-dimensional numpy array where each row represents a data point.
· centroids: A two-dimensional numpy array where each row represents a cluster centroid.

Code Description: The function begins by calculating the cosine similarity between each data point in 'data' and each centroid in 'centroids'. Cosine similarity measures the cosine of the angle between two non-zero vectors, which is an effective way to measure the similarity between two vectors. After computing the similarities, the function uses numpy's argmax function along axis 1 to find the index of the maximum value (i.e., the highest similarity) for each data point. This index corresponds to the closest centroid for that particular data point.

Note: The function assumes that both 'data' and 'centroids' are preprocessed appropriately, such as being normalized or having zero mean, which is common practice when using cosine similarity. It also assumes that the input arrays are numpy ndarrays.

Output Example: If 'data' has 5 data points and 'centroids' has 3 centroids, the function will return a one-dimensional array of length 5 with values ranging from 0 to 2 (inclusive), indicating which centroid each data point is closest to. For instance, an output of [1, 0, 2, 1, 0] would mean that the first and fourth data points are closest to the second centroid, while the second and fifth data points are closest to the first centroid, and the third data point is closest to the third centroid.
***
## FunctionDef __deduplicate_factor_dict(factor_dict)
**__deduplicate_factor_dict**: This function identifies and groups potentially duplicated factors from a dictionary of factor definitions using clustering techniques and similarity checks via an API backend.

parameters:
· factor_dict: A dictionary where keys are factor names (strings) and values are dictionaries containing details about each factor such as description, formulation, and variables.

Code Description: The function begins by checking if the input dictionary is empty. If it is, it returns an empty list. Otherwise, it transforms the dictionary into a pandas DataFrame with factors as rows and their attributes as columns. It then sorts the factor names alphabetically for consistent processing.

A mapping from each factor name to its full string representation (including description, formulation, and variables) is created. These strings are used to generate embeddings through an API backend's embedding creation method. The function determines the number of clusters (`target_k`) based on the length of the list of full strings and predefined settings for maximum input size per group.

If the total number of factors exceeds a certain threshold defined in `RD_AGENT_SETTINGS.max_input_duplicate_factor_group`, the function applies K-means clustering to group similar factors together. The embeddings are clustered using cosine similarity, and each cluster is treated as a potential group of duplicate factors.

The function then processes each group of potentially duplicated factors by calling another internal function, `__check_factor_duplication_simulate_json_mode`. This function checks for actual duplicates within each group by simulating JSON mode interactions with an API backend. It sends the factors in the group to the API and receives back any identified duplicate groups.

The results from these checks are compiled into a list of lists, where each inner list contains the names of factors that have been identified as duplicates. The function returns this list, providing a structured view of potential duplicated factor groups within the input dictionary.

Note: This function relies on external settings and API backend capabilities for generating embeddings and checking for factor duplication. It is designed to handle large sets of factors efficiently by clustering them into smaller groups before performing detailed similarity checks.

Output Example: A possible return value of this function could be a list of lists, where each inner list contains the names of factors identified as duplicates:
```
[['factor1', 'factor2'], ['factor3', 'factor4', 'factor5']]
``` 
This indicates that 'factor1' and 'factor2' are considered duplicates, as well as 'factor3', 'factor4', and 'factor5'.
## FunctionDef deduplicate_factors_by_llm(factor_dict, factor_viability_dict)
**deduplicate_factors_by_llm**: This function aims to identify and remove duplicate factors from a given dictionary of factor definitions using a multi-round deduplication process enhanced by an LLM (Large Language Model). It iteratively identifies groups of potentially duplicated factors, refines these groups if necessary, and then selects one representative factor from each group based on viability criteria.

**parameters**:
· factor_dict: A dictionary where keys are factor names (strings) and values are dictionaries containing details about each factor such as description, formulation, and variables.
· factor_viability_dict: An optional dictionary that maps factor names to their viability status. If provided, the function uses this information to determine which factors should be retained in case of duplicates.

**Code Description**: The function starts by initializing an empty list `final_duplication_names_list` to store groups of duplicate factors and setting `current_round_factor_dict` to the input `factor_dict`. It then enters a loop that runs up to 10 times, each iteration aimed at identifying and grouping duplicated factors using the helper function `__deduplicate_factor_dict`.

During each round, the function calls `__deduplicate_factor_dict` with `current_round_factor_dict` as an argument. The result is a list of lists (`duplication_names_list`) where each inner list contains names of factors that are considered duplicates.

The function then processes these groups of duplicate factors:
- If a group has fewer factors than the maximum allowed by `RD_AGENT_SETTINGS.max_output_duplicate_factor_group`, it is added to `final_duplication_names_list`.
- Otherwise, the factor names in this group are added to `new_round_names` for further processing in subsequent rounds.

If no new groups of factors require further deduplication (`new_round_names` is empty), the loop breaks. After all rounds, the function sorts `final_duplication_names_list` by the length of each inner list in descending order.

Next, the function constructs a mapping (`to_replace_dict`) that associates each duplicate factor name with a target factor name (the one deemed most viable or the first one if viability information is not provided). This step ensures that only one representative from each group of duplicates remains.

Finally, the function creates `llm_deduplicated_factor_dict`, which contains all factors from the original dictionary except those marked for replacement in `to_replace_dict`. It also excludes any factors that are deemed non-viable if `factor_viability_dict` is provided. The function returns a tuple containing this deduplicated factor dictionary and the list of duplicate groups.

**Note**: This function relies on external settings (`RD_AGENT_SETTINGS`) and an API backend for generating embeddings and checking for factor duplication. It is designed to handle large sets of factors efficiently by clustering them into smaller groups before performing detailed similarity checks.

**Output Example**: A possible return value of this function could be a tuple containing the deduplicated factor dictionary and a list of lists, where each inner list contains the names of factors identified as duplicates:
```
({
    'factor1': {'description': '...', 'formulation': '...', 'variables': '...'},
    'factor3': {'description': '...', 'formulation': '...', 'variables': '...'}
},
[['factor2', 'factor4'], ['factor5', 'factor6']])
```
This indicates that 'factor1' and 'factor3' are retained in the deduplicated dictionary, while 'factor2' and 'factor4' are considered duplicates of each other, as well as 'factor5' and 'factor6'.
## ClassDef FactorExperimentLoaderFromPDFfiles
**FactorExperimentLoaderFromPDFfiles**: This class is designed to load factor experiments from PDF files. It processes these files by loading, classifying, extracting factors, merging them into a dictionary, checking their viability, and finally deduplicating them before returning a structured dictionary of viable factors.

attributes:
· file_or_folder_path: A string representing the path to either a single PDF file or a folder containing multiple PDF files. This input is essential for specifying the source from which the factor experiments are loaded.

Code Description: The class inherits from FactorExperimentLoader and overrides its load method. Upon invocation, it begins by loading and processing PDF documents using the `load_and_process_pdfs_by_langchain` function, which likely leverages LangChain for document handling. This step is logged under the "docs" tag for tracking purposes.

Next, the reports are classified from the processed dictionary using the `classify_report_from_dict` function with a specified vote time (set to 1). The classification helps in selecting relevant reports for further processing.

The selected reports are then used to extract factors through the `extract_factors_from_report_dict` function. This extraction is logged under the "file_to_factor_result" tag, indicating the mapping of files to their respective factor results.

Following extraction, these results are merged into a single dictionary using `merge_file_to_factor_dict_to_factor_dict`, which simplifies the data structure for easier manipulation and analysis. The resulting factor dictionary is logged under the "factor_dict" tag.

The viability of each factor in the dictionary is then checked with `check_factor_viability`. This function returns two values: a list indicating the viability of each factor and a filtered version of the original factor dictionary that includes only viable factors. The filtered dictionary is logged under the "filtered_factor_dict" tag.

Finally, the class calls the load method from its parent class, FactorExperimentLoaderFromDict, passing in the filtered factor dictionary to complete the loading process.

Note: Usage points include providing a valid path to PDF files or a folder containing such files when creating an instance of this class and invoking its load method. The output is a structured dictionary representing viable factors extracted from the provided documents.

Output Example: A possible appearance of the code's return value could be:
{
    'factor1': {'description': '...', 'viability_score': 0.95, ...},
    'factor2': {'description': '...', 'viability_score': 0.87, ...},
    ...
}
### FunctionDef load(self, file_or_folder_path)
# Project Documentation

## Overview

This document provides a comprehensive guide to understanding and utilizing the [Project Name] software application. It is designed for both developers who wish to integrate this project into their applications or modify its functionality, as well as beginners looking to understand how to use it effectively.

## Table of Contents

1. **Installation**
2. **Configuration**
3. **Usage**
4. **API Documentation**
5. **Troubleshooting**
6. **Contributing**
7. **License**

---

### 1. Installation

#### Prerequisites
- Ensure you have [Prerequisite Software] installed on your system.
- Verify that your operating system meets the minimum requirements.

#### Steps to Install
1. Clone the repository from GitHub:
   ```
   git clone https://github.com/your-repo/project-name.git
   ```

2. Navigate into the project directory:
   ```
   cd project-name
   ```

3. Install dependencies using [Dependency Manager]:
   ```
   [Dependency Manager] install
   ```

4. Start the application:
   ```
   npm start
   ```

### 2. Configuration

Configuration settings are managed through a configuration file located at `config/settings.json`. Below is an overview of key configurations:

- **API_KEY**: Your unique API key for accessing external services.
- **DATABASE_URL**: Connection string to your database.

#### Example Configuration File
```json
{
  "API_KEY": "your_api_key_here",
  "DATABASE_URL": "mongodb://localhost:27017/projectdb"
}
```

### 3. Usage

#### Basic Usage
To use the application, follow these steps:

1. Start the server as described in the installation section.
2. Access the application via your web browser at `http://localhost:3000`.
3. Use the provided interface to interact with the features.

#### Advanced Features
For advanced usage, refer to the API documentation for details on available endpoints and their functionalities.

### 4. API Documentation

The API provides a set of endpoints that allow you to interact programmatically with [Project Name].

#### Base URL
```
https://api.projectname.com/v1/
```

#### Endpoints

- **GET /data**
  - Description: Fetches data from the database.
  - Parameters:
    - `limit`: Number of records to return (optional).
  - Response:
    ```json
    {
      "status": "success",
      "data": []
    }
    ```

### 5. Troubleshooting

#### Common Issues
- **Server Not Starting**: Ensure all dependencies are installed and the configuration file is correctly set up.
- **API Errors**: Check your API key and network connectivity.

#### Contact Support
For further assistance, contact support at [support-email] or visit our [Support Page].

### 6. Contributing

We welcome contributions from the community! To contribute:

1. Fork the repository on GitHub.
2. Create a new branch for your feature or bug fix.
3. Make your changes and commit them with descriptive messages.
4. Push your changes to your forked repository.
5. Submit a pull request detailing your changes.

### 7. License

This project is licensed under the [License Type] license. See the `LICENSE` file for more details.

---

Thank you for choosing [Project Name]. We hope this documentation helps you get started and make the most out of our software.
***
