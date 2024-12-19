## FunctionDef crawl_descriptions(competition, wait, force)
**crawl_descriptions**: This function retrieves detailed descriptions of a specified Kaggle competition by scraping its webpage. It returns a dictionary containing various sections of the competition's overview, including subtitles and their corresponding content.

parameters:
· competition: A string representing the name or identifier of the Kaggle competition.
· wait: A float indicating the number of seconds to wait between page loads to avoid overwhelming the server (default is 3.0 seconds).
· force: A boolean flag that, when set to True, forces a fresh scrape even if a local JSON file already exists for the competition (default is False).

Code Description: The function begins by checking if a local JSON file for the specified competition already exists and if the `force` parameter is not set. If both conditions are met, it loads and returns the data from this file to avoid unnecessary web scraping.

If no local file exists or `force` is True, the function initializes a Chrome WebDriver with predefined options and service settings. It then navigates to the competition's overview page on Kaggle. After waiting for the specified duration, it extracts the main content area of the page.

The function identifies subtitles within the overview section by locating anchor tags that link to different parts of the competition's overview. It concatenates the inner HTML of these elements to form a list of subtitles.

To accurately scrape the main contents and citation sections, the function defines an inner function `kaggle_description_css_selectors` that determines the CSS class names used for these sections on the page. Using these class names, it extracts the HTML content of each section.

The function asserts that the number of subtitles is exactly one more than the number of main contents and that the last subtitle is "Citation". It then maps each subtitle to its corresponding content in a dictionary.

Next, the function navigates to the data description page for the competition, waits again, and extracts the data description using the same CSS class name identified earlier. This description is added to the dictionary under the key "Data Description".

After completing the scraping process, the WebDriver is closed, and the collected descriptions are saved to a local JSON file named after the competition. Finally, the function returns the dictionary containing all the scraped descriptions.

Note: Usage points include ensuring that the Chrome WebDriver executable is correctly installed and accessible in the system's PATH. Additionally, the user must have internet access to fetch data from Kaggle.

Output Example: A possible appearance of the code's return value could be:
{
    "Problem Statement": "<p>Given a dataset of customer transactions, predict whether a transaction is fraudulent.</p>",
    "Evaluation Metric": "<p>The evaluation metric for this competition is F1 score.</p>",
    "Data Description": "<p>The dataset includes features such as transaction amount, time, and location.</p>",
    "Citation": "<p>Please cite the following paper when using this dataset: [citation details]</p>"
}
### FunctionDef kaggle_description_css_selectors
**kaggle_description_css_selectors**: This function retrieves CSS class names associated with the main content description and citation sections of a Kaggle webpage. It navigates through the HTML structure using Selenium's element finding methods to locate these specific sections and extracts their respective CSS classes.

parameters:
· No parameters are required for this function as it operates on predefined global variables (site_body, By).

Code Description: The function starts by locating an element with the ID "abstract" which is assumed to be the main content description section of a Kaggle webpage. It then navigates through several layers of nested HTML elements using XPath expressions to reach the desired element containing the CSS class name for the main content. This class name is extracted and stored in the variable `main_class`.

Following this, the function locates another element with the ID "citation", which represents the citation section on the same webpage. Similar to the previous process, it traverses through nested elements using XPath expressions until it reaches the element holding the CSS class for the citation content. This class name is then extracted and stored in `citation_class`.

Finally, the function returns a tuple containing both `main_class` and `citation_class`, providing the caller with the CSS classes of these two sections.

Note: Usage points include scenarios where developers need to dynamically identify or manipulate the main content description and citation sections on Kaggle webpages using their respective CSS class names. This can be particularly useful for web scraping, data extraction, or automating interactions with such pages.

Output Example: ('description-class-123', 'citation-class-456') where 'description-class-123' is the CSS class name of the main content description section and 'citation-class-456' is the CSS class name of the citation section on a specific Kaggle webpage.
***
## FunctionDef download_data(competition, local_path)
**download_data**: This function downloads data from a specified Kaggle competition to a local directory. It handles two different scenarios based on whether MLEB (Machine Learning Environment Benchmark) is used for downloading and preparing the data.

parameters:
· competition: A string representing the name of the Kaggle competition from which data needs to be downloaded.
· local_path: A string indicating the path where the downloaded data should be stored. It defaults to a predefined local data path specified in `KAGGLE_IMPLEMENT_SETTING.local_data_path`.

Code Description: The function first checks if MLEB is configured for use (`KAGGLE_IMPLEMENT_SETTING.if_using_mle_data`). If true, it sets up paths for storing zip files and the extracted competition data. It then verifies whether the necessary directories exist or are empty. If any of these conditions are met, it initializes an `MLEBDockerEnv` object to prepare the environment.

The function runs several commands within this Docker environment:
1. Prepares the MLEB environment.
2. Downloads the competition data into a designated zip files directory.
3. Copies the prepared public data from the zip files directory to the competition-specific directory.
4. Unzips any zip files found in the competition directory, extracting their contents into appropriately named directories.

For specific competitions like "new-york-city-taxi-fare-prediction", the function includes a patch to rename and organize the schema of the downloaded data to ensure consistency across different datasets.

If MLEB is not used, the function directly downloads the competition data using the Kaggle API. It checks if the zip file already exists in the specified path; if not, it uses `subprocess.run` to execute the Kaggle CLI command for downloading the dataset. If the download fails, it logs an error and raises a `KaggleError`.

After downloading, the function unzips the data using the `unzip_data` function, which extracts all contents from the zip file into the target directory. It also handles nested zip files by recursively extracting them.

Note: This function is essential for automating the process of obtaining and preparing datasets from Kaggle competitions. It supports both direct downloads via the Kaggle API and more complex setups using MLEB Docker environments, making it versatile for different data acquisition needs in machine learning projects. Developers can use this function to streamline their workflow by integrating it into scripts that require access to specific Kaggle competition datasets. Beginners should ensure they have the necessary permissions and configurations set up (e.g., Kaggle API credentials) before using this function.
## FunctionDef unzip_data(unzip_file_path, unzip_target_path)
**unzip_data**: This function extracts files from a specified zip archive into a target directory.

parameters:
· unzip_file_path: A string representing the path to the zip file that needs to be extracted.
· unzip_target_path: A string indicating the directory where the contents of the zip file should be extracted.

Code Description: The function utilizes Python's built-in zipfile module to handle zip files. It opens the specified zip file in read mode and then extracts all its contents into the designated target path using the `extractall` method. This method is straightforward and handles the extraction process efficiently, ensuring that all nested directories within the zip file are preserved during extraction.

Note: Usage points include scenarios where data needs to be decompressed from a zip format for further processing or analysis. The function assumes that both the source zip file and the target directory exist; otherwise, it may raise exceptions related to file handling. It is typically used in data preprocessing pipelines, such as after downloading datasets that are provided in compressed formats. In the context of the provided code, `unzip_data` is called by `download_data` to decompress files downloaded from Kaggle competitions or other sources, making them ready for use in machine learning workflows.
## FunctionDef leaderboard_scores(competition)
**leaderboard_scores**: This function retrieves the leaderboard scores for a specified Kaggle competition and returns them as a list of floating-point numbers.

parameters:
· competition: A string representing the name or identifier of the Kaggle competition from which to fetch the leaderboard scores.

Code Description: The function starts by importing the `KaggleApi` class from the `kaggle.api.kaggle_api_extended` module. It then creates an instance of this API and authenticates it using stored credentials. After authentication, the function calls the `competition_leaderboard_view` method on the API object, passing in the competition identifier to fetch the leaderboard data. The returned leaderboard data is a list of objects, each containing various attributes including the score. The function extracts these scores, converts them to floats, and returns them as a list.

Note: For this function to work, Kaggle credentials must be properly set up on the user's machine. Additionally, the competition identifier provided should correspond to an active or past Kaggle competition that the user has access to view.

Output Example: [0.897654321, 0.889765432, 0.885432109, ..., 0.678901234] - A list of scores from the competition's leaderboard in descending order (assuming higher scores are better).
## FunctionDef score_rank(competition, score)
**score_rank**: This function calculates the rank and rank percentage of a given score within the leaderboard scores of a specified Kaggle competition.

parameters:
· competition: A string representing the name or identifier of the Kaggle competition for which to determine the rank.
· score: A float value representing the score whose rank is to be determined on the competition's leaderboard.

Code Description: The function begins by retrieving the leaderboard scores for the specified competition using the `leaderboard_scores` function. It then checks if the list of scores is in ascending or descending order. If the scores are in ascending order (where a higher index indicates a better score), it uses the `bisect_right` method to find the position where the given score would fit in the sorted list, which corresponds to its rank minus one. If the scores are in descending order (more common in competitions where higher scores indicate better performance), the function reverses the list and again uses `bisect_right` to find the appropriate position. The rank is then adjusted by adding one to convert from zero-based indexing to a more human-readable format. Finally, the function calculates the rank percentage by dividing the rank by the total number of scores and multiplying by 100.

Note: For this function to operate correctly, Kaggle credentials must be properly configured on the user's machine, and the competition identifier should correspond to an active or past Kaggle competition that is accessible to the user. The function assumes that the leaderboard scores are either entirely in ascending or descending order.

Output Example: (15, 23.4) - This indicates that the given score ranks 15th out of all participants and represents approximately 23.4% of the total rankings on the leaderboard.
## FunctionDef download_notebooks(competition, local_path, num)
**download_notebooks**: This function downloads a specified number of Jupyter notebooks from a Kaggle competition based on their leaderboard scores.

**parameters**:
· competition: A string representing the name of the Kaggle competition from which to download notebooks.
· local_path: An optional string specifying the local directory where the downloaded notebooks should be saved. Defaults to a path constructed using the `KAGGLE_IMPLEMENT_SETTING.local_data_path` and appending `/notebooks`.
· num: An optional integer indicating the number of top-scoring notebooks to download. Defaults to 15.

**Code Description**: The function begins by constructing a local data path where the downloaded notebooks will be stored, combining the provided or default `local_path` with the competition name. It then initializes and authenticates an instance of Kaggle's API using `KaggleApi`.

To determine whether to sort the leaderboard in ascending or descending order based on scores, the function retrieves the leaderboard for the specified competition. It calculates the difference between the highest and lowest scores; if this difference is positive, it indicates that higher scores are better, and the notebooks should be sorted in descending order (`scoreDescending`). Otherwise, they are sorted in ascending order (`scoreAscending`).

The function then fetches a list of kernels (notebooks) associated with the competition, sorting them according to the determined sort order and limiting the number of results to `num`. For each notebook in this list, it extracts the author's username from the notebook reference and uses the Kaggle API to download the notebook to a subdirectory within the local data path named after the author.

Finally, the function prints a message indicating how many notebooks were downloaded for the competition and specifies the sort order used.

**Note**: Usage points include ensuring that the Kaggle API credentials are properly configured in the environment before running this function. Additionally, users should verify that they have the necessary permissions to access the specified competition's data on Kaggle.
## FunctionDef notebook_to_knowledge(notebook_text)
**notebook_to_knowledge**: This function processes a string containing notebook text (which could be from Jupyter notebooks or Python scripts) to generate knowledge content by leveraging prompts defined in an external YAML file and utilizing an API backend for chat completion.

**parameters**:
· notebook_text: A string representing the content of a notebook, formatted with code and markdown sections as appropriate. This input is expected to be pre-processed into a specific format where each cell (markdown or code) is enclosed within triple backticks followed by the language identifier (e.g., "```markdown" for markdown cells and "```code" for code cells).

**Code Description**: The function begins by initializing a `Prompts` object with the path to a YAML file containing predefined prompts. It then constructs two types of prompts: a system prompt and a user prompt. The system prompt is generated using a template from the YAML file, while the user prompt includes the notebook text provided as an argument. These prompts are used to interact with an API backend that generates chat completions based on the input. The `APIBackend` class's method `build_messages_and_create_chat_completion` is called with the constructed system and user prompts, along with a flag indicating whether the response should be in JSON format (set to False here). The function returns the generated knowledge content from the API.

**Note**: Usage points include scenarios where notebook content needs to be transformed into structured or summarized knowledge. This could be useful for documentation generation, code analysis, or educational purposes. The function assumes that the input text is correctly formatted as per the expected structure (markdown and code cells enclosed in triple backticks).

**Output Example**: A possible appearance of the code's return value could be a detailed explanation or summary of the notebook content, including insights about the code logic, purpose of markdown sections, and any other relevant information derived from the input text. For instance:
```
The provided notebook contains an implementation of a machine learning model for image classification using TensorFlow. The dataset used is CIFAR-10, which consists of 60,000 32x32 color images in 10 different classes. The code includes data preprocessing steps such as normalization and augmentation, followed by the definition of a convolutional neural network architecture. Training parameters like batch size and number of epochs are set to optimize performance. Markdown sections provide explanations for each step of the process, including theoretical background on image classification techniques.
```
## FunctionDef convert_notebooks_to_text(competition, local_path)
**convert_notebooks_to_text**: This function processes Jupyter notebooks (both .ipynb and .irnb files) and Python scripts (.py files) from a specified local directory, converting their content into text format. The converted text is then processed further to generate knowledge content using an external function.

**parameters**:
· competition: A string representing the name of the Kaggle competition. This parameter helps in locating the specific folder within the local data path that contains the notebooks and scripts related to the competition.
· local_path: An optional string specifying the local directory where the notebooks and scripts are stored. It defaults to a predefined path based on the project's settings.

**Code Description**: The function starts by constructing the full path to the competition-specific folder within the local data directory. It initializes a counter, `converted_num`, to keep track of how many files have been processed.

The function then iterates over all .ipynb and .irnb files in the specified directory and its subdirectories. For each notebook file, it opens the file, reads its content using the nbformat library (ensuring compatibility with version 4), and initializes an empty list to store the text representation of each cell.

For each cell in the notebook, the function checks if the cell type is markdown or code. Markdown cells are enclosed within triple backticks followed by "markdown", while code cells are similarly enclosed but labeled as "code". This formatted string is then joined into a single text document representing the entire notebook.

The resulting text from the notebook is passed to the `notebook_to_knowledge` function, which processes it further to generate knowledge content. The processed text is written to a new file with the same name as the original notebook but with a .txt extension.

Next, the function performs a similar operation for all .py files in the directory and its subdirectories. Each Python script is read into a string, enclosed within triple backticks labeled as "code", and then processed by `notebook_to_knowledge`. The output is written to a corresponding .txt file.

After processing all files, the function prints the total number of notebooks and scripts that have been converted to text format.

**Note**: This function is particularly useful for converting structured notebook content into a more accessible text format, which can then be used for documentation, analysis, or educational purposes. It assumes that the input files are correctly formatted Jupyter notebooks or Python scripts. The use of `notebook_to_knowledge` ensures that the converted text is not only a direct representation of the original files but also enriched with additional knowledge content derived from external prompts and API processing.
## FunctionDef collect_knowledge_texts(local_path)
**collect_knowledge_texts**: This function aggregates knowledge texts from a specified local directory structure, organizing them by competition names.

parameters:
· local_path: A string representing the path to the local directory where Kaggle competition notebooks are stored. It defaults to the value of `KAGGLE_IMPLEMENT_SETTING.local_data_path`.

Code Description: The function begins by constructing a Path object that points to the 'notebooks' subdirectory within the provided or default local path. It initializes an empty dictionary, `competition_knowledge_texts_dict`, which will store lists of knowledge texts keyed by competition names.

The function then iterates over each directory in the 'notebooks' directory, treating each as a separate competition. For each competition directory, it searches recursively for all files with a '.txt' extension using the glob method. Each found text file is read into memory as a string and appended to a list of knowledge texts specific to that competition.

After processing all text files within a competition's directory, the function adds an entry to `competition_knowledge_texts_dict` where the key is the name of the competition (derived from the directory name) and the value is the list of collected knowledge texts for that competition. This process repeats for each competition directory found in 'notebooks'.

Note: The function assumes that all relevant text files are stored within subdirectories of the 'notebooks' directory, with each subdirectory representing a different Kaggle competition.

Output Example: 
{
    "competition1": [
        "This is the first knowledge text for competition 1.",
        "Here is another piece of knowledge from competition 1."
    ],
    "competition2": [
        "First knowledge text for competition 2.",
        "Second knowledge text for competition 2.",
        "Third knowledge text for competition 2."
    ]
}
