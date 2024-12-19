## ClassDef KGScenario
**KGScenario**: This class represents a scenario for handling Kaggle competitions, automating the process of feature engineering and model tuning. It inherits from a base `Scenario` class and initializes various attributes to manage competition details, data preprocessing, and automated decision-making processes.

**attributes**:
· competition: A string representing the name of the Kaggle competition.
· competition_descriptions: A description crawled from the competition page.
· input_shape: The shape of the input data, initialized as None.
· competition_type: Type of the competition, determined after analyzing the competition description.
· competition_description: Detailed description of the competition.
· target_description: Description of the target variable in the competition.
· competition_features: Features available for use in the competition.
· submission_specifications: Specifications for submitting predictions to Kaggle.
· model_output_channel: The number of output channels required by the model, typically 1.
· evaluation_desc: Description of how the submissions are evaluated.
· leaderboard: Scores from the competition's leaderboard.
· evaluation_metric_direction: Boolean indicating whether a higher score is better (True) or worse (False).
· vector_base: An instance of `KaggleExperienceBase` used for storing and retrieving experience data, initialized based on settings.
· mini_case: A flag to indicate if a mini-case scenario is being run.
· if_action_choosing_based_on_UCB: Boolean indicating whether Upper Confidence Bound (UCB) is used for action selection.
· if_using_graph_rag: Boolean indicating whether Graph-based Retrieval-Augmented Generation (RAG) is used.
· if_using_vector_rag: Boolean indicating whether Vector-based RAG is used.
· action_counts: A dictionary to keep track of the number of times each action has been taken.
· reward_estimates: A dictionary to estimate rewards for different actions, initialized with default values.
· confidence_parameter: Parameter used in UCB algorithm, set to 1.0 by default.
· initial_performance: Initial performance metric, set to 0.0 by default.

**Code Description**: The `KGScenario` class initializes several attributes related to the competition and its data. It uses external functions like `crawl_descriptions` and `leaderboard_scores` to gather information about the competition. The `_analysis_competition_description` method analyzes the competition description using a system prompt and user prompt, which are rendered from templates defined in `prompt_dict`. This analysis helps populate attributes such as `competition_type`, `competition_description`, etc.

The class provides methods for retrieving detailed descriptions of the competition (`get_competition_full_desc`), generating background information about the scenario (`background`), obtaining information about the source data (`source_data`), and specifying output formats and interfaces for feature and model code (`output_format`, `interface`). It also includes a method to simulate the behavior of the feature and model code (`simulator`) and provides a rich, styled description of the Kaggle agent's objectives and processes (`rich_style_description`).

The `get_scenario_all_desc` method generates a comprehensive description of the scenario based on the task and filtered tags provided. This description includes background information, source data details, submission specifications, interfaces, output formats, and simulators relevant to the specified tag.

**Note**: The class uses external settings from `KAGGLE_IMPLEMENT_SETTING`, which should be properly configured before using this class. It also relies on other classes and functions such as `APIBackend`, `KGFactorExperiment`, and `prompt_dict` for its operations.

**Output Example**: A possible output of the `get_competition_full_desc` method could be:

Competition Type: Regression
    Competition Description: Predict house prices based on various features.
    Target Description: SalePrice - the final price each home was sold for.
    Competition Features: MSSubClass, MSZoning, LotFrontage, LotArea, Street, Alley, LotShape, LandContour, Utilities, ...
    Submission Specifications: Submit a CSV file with Id and SalePrice columns.
    Model Output Channel: 1
    Evaluation Descriptions: Root Mean Squared Logarithmic Error (RMSLE)
    Is the evaluation metric the higher the better: False

This output provides a clear overview of the competition's requirements and metrics, aiding in the development of appropriate models and features.
### FunctionDef __init__(self, competition)
**__init__**: Initializes an instance of the KGScenario class by setting up essential attributes related to a Kaggle competition.

parameters:
· competition: A string representing the name or identifier of the Kaggle competition.

Code Description: The __init__ method begins by calling the superclass's initializer using `super().__init__()`. It then initializes several key attributes:

- `self.competition`: Stores the name or identifier of the competition.
- `self.competition_descriptions`: Obtains detailed descriptions of the competition by invoking the `crawl_descriptions` function with the competition name as an argument.
- `self.input_shape`: Initialized to None, intended for storing the shape of input data (not set in this initializer).
- `self.competition_type`, `self.competition_description`, `self.target_description`, `self.competition_features`, `self.submission_specifications`, `self.model_output_channel`, `self.evaluation_desc`: These attributes are initialized to None and will be populated by the `_analysis_competition_description` method.
- `self.leaderboard`: Retrieves leaderboard scores for the competition using the `leaderboard_scores` function with the competition name as an argument.
- `self.evaluation_metric_direction`: Determines whether the evaluation metric improves when increasing or decreasing, based on comparing the first and last values in the leaderboard.
- `self.vector_base`: Initialized to None, intended for storing a vector-based knowledge base if certain conditions are met.
- `self.mini_case`, `self.if_action_choosing_based_on_UCB`, `self.if_using_graph_rag`, `self.if_using_vector_rag`: These attributes are set based on values from the `KAGGLE_IMPLEMENT_SETTING` configuration object.

The method checks if vector-based Retrieval-Augmented Generation (RAG) is enabled and a path for RAG is specified in the settings. If both conditions are true, it initializes `self.vector_base` with an instance of `KaggleExperienceBase`, sets its path to a timestamped filename, and saves it.

Several dictionaries related to action counts and reward estimates are initialized:
- `self.action_counts`: A dictionary mapping each action from `KG_ACTION_LIST` to 0.
- `self.reward_estimates`: A dictionary mapping each action from `KG_ACTION_LIST` to an initial estimate of 0.0, with specific actions ("Feature processing" and "Feature engineering") having higher estimates.

Finally, the method sets `self.confidence_parameter` to 1.0 and initializes `self.initial_performance` to 0.0.

Note: Usage points.
The __init__ method is crucial for setting up a new instance of the KGScenario class with all necessary attributes related to a specific Kaggle competition. It ensures that essential data such as competition descriptions, leaderboard scores, and configuration settings are properly initialized. This setup facilitates subsequent operations within the scenario, including analysis and decision-making processes based on the competition's requirements and structure.
***
### FunctionDef _analysis_competition_description(self)
**_analysis_competition_description**: This function analyzes the competition description by generating a system prompt and user prompt using Jinja2 templates. It then sends these prompts to an API backend for processing, expecting a JSON response that contains detailed information about the competition type, description, target, features, submission specifications, evaluation details, and more.

parameters:
· None: The function does not take any parameters directly but relies on instance variables such as `self.competition_descriptions`, `self.source_data`, and `self.evaluation_metric_direction`.

Code Description: Detailed analysis and description.
The function begins by creating a system prompt using the Jinja2 templating engine. It retrieves the template from a dictionary named `prompt_dict` under the key `"kg_description_template"` and the subkey `"system"`. The `StrictUndefined` environment is used to ensure that any undefined variables in the template will raise an error, preventing silent failures.

Next, it constructs a user prompt using the same templating engine. This time, it uses the template found at `prompt_dict["kg_description_template"]["user"]`. The user prompt includes several variables: `competition_descriptions`, `raw_data_information`, and `evaluation_metric_direction`. These are provided by instance variables of the class (`self.competition_descriptions`, `self.source_data`, and `self.evaluation_metric_direction` respectively).

The function then calls a method on an instance of `APIBackend` to build messages from these prompts and create a chat completion. The `json_mode=True` parameter indicates that the response should be in JSON format.

After receiving the response, it is parsed from a string into a Python dictionary using `json.loads`. From this dictionary, the function extracts several pieces of information about the competition:
- Competition Type
- Competition Description
- Target Description
- Competition Features
- Submission Specifications
- Model Output Channel (defaulting to 1 if not provided)
- Evaluation Description

Each piece of extracted information is stored in corresponding instance variables (`self.competition_type`, `self.competition_description`, etc.). If any expected key is missing from the JSON response, a default value is assigned.

Note: Usage points.
This function is crucial for initializing and populating several attributes of the `KGScenario` class with detailed information about the competition. It leverages external APIs to analyze and interpret competition descriptions, which can be complex and varied. By automating this process, it ensures that all necessary details are captured in a structured format, making them easily accessible for further analysis or decision-making within the scenario.

The function is called during the initialization of the `KGScenario` class, ensuring that these attributes are set up as soon as an instance of the class is created. This setup facilitates subsequent operations that depend on having a clear understanding of the competition's requirements and structure.
***
### FunctionDef get_competition_full_desc(self)
**get_competition_full_desc**: This function generates a comprehensive description of a Kaggle competition by compiling various attributes into a formatted string. It provides details about the competition type, description, target, features, submission rules, output channel, evaluation criteria, and whether a higher or lower score is preferable for the evaluation metric.

parameters:
· None: The function does not accept any parameters. It relies on instance variables of the class where it is defined to generate the description.

Code Description: Detailed analysis and description.
The function constructs a string that includes several key pieces of information about a Kaggle competition. These details are derived from attributes of the object (presumably an instance of a class related to competitions). The attributes include:
- `competition_type`: Specifies the type of competition, such as regression or classification.
- `competition_description`: A brief description of what the competition entails.
- `target_description`: Details about the target variable that participants are trying to predict.
- `competition_features`: Information about the features provided in the dataset for making predictions.
- `submission_specifications`: Guidelines on how submissions should be formatted and submitted.
- `model_output_channel`: Indicates where or how the model's output is expected to be delivered.
- `evaluation_desc`: Describes the criteria used to evaluate the performance of the models.
- `evaluation_metric_direction`: A boolean indicating whether a higher score in the evaluation metric is better (True) or if a lower score is preferable (False).

The function determines the direction of the evaluation metric by checking the value of `evaluation_metric_direction`. If it is True, it sets `evaluation_direction` to "higher the better"; otherwise, it sets it to "lower the better". This information is then included in the final string.

Note: Usage points.
This function is useful for generating a detailed summary of a competition's requirements and evaluation criteria. It can be used to inform participants about what they need to know before starting their work on the competition. The function assumes that all necessary attributes are properly set within the object instance.

Output Example: Mock up a possible appearance of the code's return value.
Competition Type: Regression
    Competition Description: Predict future sales based on historical data.
    Target Description: Total number of items sold in each store for each product category.
    Competition Features: Date, store ID, product category, promotional activities.
    Submission Specifications: Submit a CSV file with the predicted sales figures.
    Model Output Channel: Upload predictions to the designated Kaggle competition page.
    Evaluation Descriptions: Root Mean Squared Error (RMSE) between actual and predicted sales.
    Is the evaluation metric the higher the better: lower the better
***
### FunctionDef background(self)
**background**: This function generates a background description for a Kaggle competition scenario by rendering a template with specific details about the competition, including its type, description, target variable, features, submission specifications, evaluation criteria, and whether the evaluation metric is to be maximized or minimized.

parameters:
· None: The function does not accept any parameters directly. It relies on instance variables of the class `KGScenario`.

Code Description: Detailed analysis and description.
The function starts by retrieving a template string from a dictionary named `prompt_dict` using the key `"kg_background"`. This template likely contains placeholders for various pieces of information about the Kaggle competition.

Next, it constructs the path to a training script file specific to the competition. The path is built relative to the current file's location, incorporating the name of the competition (retrieved from `KAGGLE_IMPLEMENT_SETTING.competition`) and appending `"train.py"` as the filename. It then reads the content of this file into a string variable named `train_script`.

The function uses Jinja2 templating to render the background template. It creates an environment with strict undefined behavior, meaning any missing variables in the template will raise an error rather than silently failing or being replaced by a default value. The template is then rendered using various instance variables of the class `KGScenario`:
- `train_script`: The content of the training script file.
- `competition_type`: A description of the type of competition (e.g., classification, regression).
- `competition_description`: A detailed description of the competition's objectives and context.
- `target_description`: Information about the target variable that participants are expected to predict.
- `competition_features`: Details about the features provided in the dataset for modeling.
- `submission_specifications`: Guidelines on how to format submissions.
- `evaluation_desc`: Description of the evaluation metric used to assess model performance.
- `evaluate_bool`: A boolean indicating whether the evaluation metric should be maximized (`True`) or minimized (`False`).

The rendered template, which now includes all the specific details about the competition, is returned as a string.

Note: Usage points.
This function is typically called internally within the class to generate part of the scenario description. It provides essential context for participants in a Kaggle competition by detailing what they need to know before starting their work on the dataset and model development.

Output Example: Mock up a possible appearance of the code's return value.
```
The objective of this competition is to predict housing prices based on various features such as location, size, and age. Participants are provided with a dataset containing historical sales data. The target variable is 'SalePrice', which represents the final price at which a property was sold.

Participants should submit their predictions in a CSV file with two columns: 'Id' and 'SalePrice'. Each row corresponds to an entry in the test set, and the 'SalePrice' column contains the predicted sale prices. The evaluation metric used is Root Mean Squared Logarithmic Error (RMSLE), which will be minimized during scoring.
```
***
### FunctionDef source_data(self)
**source_data**: This function retrieves or generates the source data required for a Kaggle competition scenario. It checks if preprocessed validation data exists; if not, it preprocesses the data, saves it to disk, and then loads the validation dataset.

parameters:
· None: The function does not take any parameters directly but relies on instance variables such as `self.competition` and `KAGGLE_IMPLEMENT_SETTING.local_data_path`.

Code Description: Detailed analysis and description.
The function starts by constructing a path to the data folder specific to the competition using the local data path setting and the name of the competition. It then checks if the validation dataset file (`X_valid.pkl`) exists in this directory.

If the validation dataset does not exist, the function initializes an instance of `KGFactorExperiment` with an empty list (the purpose of which is unclear from the provided code snippet). It calls the `generate_preprocess_data` method on the experiment's workspace to obtain preprocessed training and validation datasets along with other necessary data.

The generated datasets are then saved in the competition-specific directory using the `pickle.dump` function, ensuring that all required data is stored for future use. After saving the data, the function loads the validation dataset (`X_valid.pkl`) from disk using `pd.read_pickle`.

A hardcoded check is performed to return a specific string if the competition name matches "feedback-prize-english-language-learning". Otherwise, it generates a detailed information string about the validation dataset by calling the `info` method on the DataFrame and capturing its output. This information includes details about the dataset's structure without showing value counts.

The function also sets an instance variable `self.input_shape` to the shape of the validation dataset before returning the generated or hardcoded data information string.

Note: Usage points.
This function is crucial for ensuring that the necessary preprocessed data is available for analysis and experimentation. It abstracts away the details of data preprocessing, storage, and retrieval, making it easier to work with competition datasets in a standardized manner. The function is called by other methods within the `KGScenario` class, such as `_analysis_competition_description` and `common_description`, to provide detailed information about the dataset.

Output Example: Mock up a possible appearance of the code's return value.
For a competition other than "feedback-prize-english-language-learning", the output might look like this:
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 1000 entries, 0 to 999
Data columns (total 20 columns):
 #   Column  Non-Null Count  Dtype  
---  ------  --------------  -----  
 0   feature1  1000 non-null   float64
 1   feature2  1000 non-null   int64  
...
 19  target    1000 non-null   float64
dtypes: float64(15), int64(5)
memory usage: 152.8 KB
```
This output provides a summary of the dataset's structure, including the number of entries, column names, data types, and memory usage.
***
### FunctionDef output_format(self, tag)
**output_format**: This function returns a string detailing the required output format for either feature code or model code, based on the specified tag. If no tag is provided, it returns both formats.

parameters:
· tag: An optional parameter that can be None, "feature", or "model". It specifies which type of output format to return. If None, both feature and model formats are returned.

Code Description: The function first asserts that the provided tag is either None, "feature", or "model". It then constructs two strings containing the required output formats for feature code and model code respectively. For the model code, it uses a Jinja2 Environment to render a template string with the channel attribute of the current instance. Depending on the value of the tag parameter, the function returns either the feature format, the model format, or both concatenated together.

Note: This function is typically used in scenarios where specific output formats are required for different components of a machine learning pipeline, such as features and models. It ensures that developers adhere to these formats when writing their code.

Output Example: If called with tag="feature", it might return:
The feature code should output following the format:
[Description of the expected feature output format]

If called with tag="model", it might return:
The model code should output following the format:
[Description of the expected model output format, possibly including a channel-specific detail]
***
### FunctionDef interface(self, tag)
**interface**: This function provides a standardized interface description for either feature engineering or model implementation based on the specified machine learning tag.

parameters:
· tag: A string indicating the type of interface needed. It can be one of "feature", "XGBoost", "RandomForest", "LightGBM", "NN", or None. If None, it returns both feature and model interfaces.

Code Description: The function starts by asserting that the provided tag is either None or one of the specified machine learning models or "feature". It then defines a string `feature_interface` which contains the interface description for feature engineering tasks. This description is fetched from a dictionary named `prompt_dict` under the key 'kg_feature_interface'. If the tag is "feature", the function returns this feature interface string.

For other tags, it constructs a model interface string by rendering a template found in `prompt_dict['kg_model_interface']`. The rendering process uses Jinja2's Environment and StrictUndefined to safely substitute the tag into the template. This ensures that any undefined variables within the template will raise an error, preventing silent failures.

If the tag is None, the function concatenates both the feature interface and the model interface strings with a newline in between and returns this combined string. Otherwise, it returns only the model interface corresponding to the specified tag.

Note: Usage points include calling this function with different tags to retrieve specific interface guidelines for either feature engineering or various machine learning models. This is particularly useful when setting up new projects or ensuring consistency across multiple developers working on the same project.

Output Example: A possible return value of this function when called with `tag="XGBoost"` could be:
The model code should follow the interface:
# XGBoost Model Interface
## Required Methods
- fit(X, y): Trains the model using the provided dataset.
- predict(X): Predicts target values for the given input features.

## Expected Parameters
- X: A numpy array or pandas DataFrame containing feature data.
- y: A numpy array or pandas Series containing target values.
***
### FunctionDef simulator(self, tag)
**simulator**: This function generates a string containing simulation instructions for either feature code, model code, or both based on an optional tag parameter.

parameters:
· tag: An optional parameter that specifies which type of code to simulate. It can be None (default), "feature", or "model".

Code Description: The simulator function is designed to prepare and return a string containing simulation instructions for either feature code, model code, or both. It first asserts that the provided tag is one of the allowed values: None, "feature", or "model". If the tag is not within this set, an AssertionError will be raised.

The function defines two strings:
- kg_feature_simulator: This string contains a static message indicating that feature code will be sent to the simulator, followed by the content from prompt_dict["kg_feature_simulator"].
- kg_model_simulator: This string also starts with a static message about sending model code to the simulator. However, it uses Jinja2 templating (Environment and StrictUndefined) to render the content of prompt_dict["kg_model_simulator"], incorporating self.submission_specifications into the template.

Depending on the value of the tag parameter:
- If tag is None, the function returns a combined string containing both kg_feature_simulator and kg_model_simulator.
- If tag is "feature", only kg_feature_simulator is returned.
- If tag is "model", only kg_model_simulator is returned.

Note: The function assumes that prompt_dict and self.submission_specifications are defined elsewhere in the codebase. Users of this function should ensure these variables are properly initialized before calling simulator.

Output Example: 
If tag is None, a possible return value could be:
"The feature code will be sent to the simulator:
[content from prompt_dict['kg_feature_simulator']]

The model code will be sent to the simulator:
[rendered content of prompt_dict['kg_model_simulator'] using self.submission_specifications]"

If tag is "feature", a possible return value could be:
"The feature code will be sent to the simulator:
[content from prompt_dict['kg_feature_simulator']]"

If tag is "model", a possible return value could be:
"The model code will be sent to the simulator:
[rendered content of prompt_dict['kg_model_simulator'] using self.submission_specifications]"
***
### FunctionDef rich_style_description(self)
**rich_style_description**: This function returns a detailed string formatted as markdown, providing an overview of the Kaggle Agent's capabilities, including its process for automated feature engineering and model tuning evolution within a Kaggle competition context.

parameters:
· None: The function does not accept any parameters.

Code Description: The function constructs a descriptive text that outlines the purpose and functionality of the Kaggle Agent. It starts with an introduction to the agent's iterative process involving hypothesis generation, action selection, code implementation, validation, and feedback utilization. Following this, it provides specific details about the current Kaggle competition the agent is participating in by referencing `self.competition`. The text then delves into the two main components of the automated R&D process: Research (R) and Development (D). It explains that the Research phase involves the continuous iteration of ideas and hypotheses along with learning and knowledge construction. Meanwhile, the Development phase focuses on evolving code generation, refining models, generating features, and automating their implementation and testing. Lastly, it states the primary objective of the agent: to optimize performance metrics within the validation set or Kaggle Leaderboard through autonomous research and development.

Note: This function is designed to be called when a detailed description of the scenario's objectives and processes is required, such as in logging or user interfaces.

Output Example: 
### Kaggle Agent: Automated Feature Engineering & Model Tuning Evolution

#### [Overview](#_summary)

In this scenario, our automated system proposes hypothesis, choose action, implements code, conducts validation, and utilizes feedback in a continuous, iterative process.

#### Kaggle Competition info

Current Competition: [titanic](https://www.kaggle.com/competitions/titanic)

#### [Automated R&D](#_rdloops)

- **[R (Research)](#_research)**
- Iteration of ideas and hypotheses.
- Continuous learning and knowledge construction.

- **[D (Development)](#_development)**
- Evolving code generation, model refinement, and features generation.
- Automated implementation and testing of models/features.

#### [Objective](#_summary)

To automatically optimize performance metrics within the validation set or Kaggle Leaderboard, ultimately discovering the most efficient features and models through autonomous research and development.
***
### FunctionDef get_scenario_all_desc(self, task, filtered_tag, simple_background)
**get_scenario_all_desc**: This function generates a comprehensive description of a scenario based on optional parameters such as task type, filtered tag, and whether to use a simplified background. It compiles various sections including background information, dataset details, expected output specifications, interface guidelines, output format, and simulator usage into a single string.

**parameters**:
· task: An instance of the Task class or None. This parameter is currently not utilized within the function but might be intended for future functionality.
· filtered_tag: A string representing a specific tag to filter the description content or None if no filtering is required. The recognized tags are "hypothesis_and_experiment", "feedback", and "feature". Any other tag will default to a general model interface, output format, and simulator.
· simple_background: A boolean indicating whether to use a simplified background description or not. This parameter is currently not utilized within the function but might be intended for future functionality.

**Code Description**: The function defines several inner functions to generate different parts of the scenario description:
- `common_description()`: Returns a string containing the background, source dataset, and submission specifications.
- `interface(tag)`: Generates a section detailing the interface guidelines based on the provided tag.
- `output(tag)`: Produces a section describing the expected output format according to the given tag.
- `simulator(tag)`: Provides information about the simulator that can be used for testing solutions, tailored by the tag.

The main logic of the function checks the value of `filtered_tag`:
- If `filtered_tag` is None, it returns a full description including all sections without any filtering.
- For specific tags ("hypothesis_and_experiment", "feedback"), it returns only the common background and simulator information.
- For the tag "feature", it includes the common background, feature-specific interface, output format, and simulator details.
- For any other tag value, it defaults to providing the common background, a general model interface, output format, and simulator.

**Note**: The function is designed to be flexible in generating scenario descriptions based on different tags. However, the `task` and `simple_background` parameters are not currently used within the function's logic and may be placeholders for future enhancements.

**Output Example**: A possible return value of this function could look like:

```
------Background of the scenario------
This scenario involves predicting customer churn in a telecommunications company based on historical data.

------The source dataset you can use to generate the features------
The dataset includes customer demographics, account information, and service usage details.

------The expected output & submission format specifications------
Your model should predict whether each customer will churn within the next month. The submission file should be a CSV with two columns: 'customer_id' and 'churn_probability'.

------The interface you should follow to write the runnable code------
To implement your solution, create a Python function that takes a DataFrame as input and returns a DataFrame with predictions.

------The output of your code should be in the format------
A pandas DataFrame with columns ['customer_id', 'churn_probability'] where churn_probability is a float between 0 and 1.

------The simulator user can use to test your solution------
Use the provided simulate_churn_predictions function to evaluate your model's performance on unseen data.
```
#### FunctionDef common_description
**common_description**: This function generates a comprehensive description of a Kaggle competition scenario by combining background information, details about the source dataset, and specifications for expected output and submission format.

parameters:
· None: The function does not accept any parameters directly. It relies on instance variables of the class `KGScenario`.

Code Description: Detailed analysis and description.
The function constructs a detailed string that includes three main sections:
1. **Background of the scenario**: This section is populated by calling the `background` method of the `KGScenario` class, which generates a background description for the competition. The background information typically includes details about the type of competition, its objectives, the target variable, features provided in the dataset, submission guidelines, evaluation criteria, and whether the evaluation metric should be maximized or minimized.
2. **The source dataset you can use to generate the features**: This section is populated by calling the `source_data` method of the `KGScenario` class. It provides information about the dataset used for generating features. If preprocessed validation data does not exist, it preprocesses the data, saves it, and then loads the validation dataset. The function returns a detailed description of the dataset's structure or a hardcoded string if the competition name matches "feedback-prize-english-language-learning".
3. **The expected output & submission format specifications**: This section is directly populated using the `submission_specifications` instance variable of the `KGScenario` class, which contains guidelines on how to format submissions.

Note: Usage points.
This function is typically called internally within the class to generate a comprehensive description of the Kaggle competition scenario. It provides essential context for participants by detailing what they need to know before starting their work on the dataset and model development.

Output Example: Mock up a possible appearance of the code's return value.
```
------Background of the scenario------
The objective of this competition is to predict housing prices based on various features such as location, size, and age. Participants are provided with a dataset containing historical sales data. The target variable is 'SalePrice', which represents the final price at which a property was sold.

Participants should submit their predictions in a CSV file with two columns: 'Id' and 'SalePrice'. Each row corresponds to an entry in the test set, and the 'SalePrice' column contains the predicted sale prices. The evaluation metric used is Root Mean Squared Logarithmic Error (RMSLE), which will be minimized during scoring.

------The source dataset you can use to generate the features------
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 1000 entries, 0 to 999
Data columns (total 20 columns):
 #   Column  Non-Null Count  Dtype  
---  ------  --------------  -----  
 0   feature1  1000 non-null   float64
 1   feature2  1000 non-null   int64  
...
 19  target    1000 non-null   float64
dtypes: float64(15), int64(5)
memory usage: 152.8 KB

------The expected output & submission format specifications------
Participants should submit their predictions in a CSV file with two columns: 'Id' and 'SalePrice'. Each row corresponds to an entry in the test set, and the 'SalePrice' column contains the predicted sale prices.
```
***
#### FunctionDef interface(tag)
**interface**: This function generates a formatted string that outlines the interface guidelines for writing runnable code. It accepts an optional tag parameter, which can be used to specify particular sections or types of interfaces.

parameters:
· tag: A string or None type value. This parameter is intended to filter or specify the type of interface description needed. If no specific tag is provided (i.e., it's None), the function will return a general interface guideline.

Code Description: The function constructs and returns a multi-line string that starts with a header indicating its purpose, which is to provide guidelines for writing runnable code. It then calls another method on the same object (`self.interface(tag)`) to retrieve the specific interface details based on the provided tag. This method call suggests that there might be additional logic within the class to handle different types of interfaces depending on the tag value.

Note: Developers should ensure they provide a valid string or None for the `tag` parameter when calling this function. Providing an invalid type could lead to runtime errors if not handled elsewhere in the codebase. Beginners are advised to start with a None value to understand the general guidelines before exploring specific tagged interfaces.

Output Example: 
------The interface you should follow to write the runnable code------
Here are the basic rules for writing your code:
1. Use clear variable names.
2. Include comments where necessary.
3. Ensure all functions have docstrings describing their purpose and parameters.
***
#### FunctionDef output(tag)
**output**: This function generates a formatted string that outlines the required output format for either feature code or model code based on the specified tag. If no tag is provided, it returns both formats.

parameters:
· tag: An optional parameter that can be None, "feature", or "model". It specifies which type of output format to return. If None, both feature and model formats are returned.

Code Description: The function constructs a string detailing the required output format by calling another method named `output_format` with the provided tag as an argument. This method returns a detailed description of the expected output format for either feature code or model code, depending on the value of the tag parameter. If no tag is specified (i.e., it is None), both formats are returned.

Note: This function is useful in scenarios where specific output formats are required for different components of a machine learning pipeline, such as features and models. It ensures that developers adhere to these formats when writing their code.

Output Example: If called with tag="feature", the function might return:
------The output of your code should be in the format------
The feature code should output following the format:
[Description of the expected feature output format]

If called with tag="model", it might return:
------The output of your code should be in the format------
The model code should output following the format:
[Description of the expected model output format, possibly including a channel-specific detail]
***
#### FunctionDef simulator(tag)
**simulator**: This function generates a formatted string that includes a description of how a simulator user can test their solution. The output is tailored based on an optional tag parameter.

parameters:
· tag: A string or None type value that specifies additional context or configuration for the simulator. If provided, it influences the content returned by `self.simulator(tag)`.

Code Description: The function takes one parameter, `tag`, which can either be a string or None. It returns a multi-line string where the first line is a static message indicating the purpose of the simulator. The second line dynamically includes the result of calling another method on the same object (`self.simulator(tag)`), passing along the `tag` parameter. This implies that the actual content describing how to use the simulator depends on the implementation of `self.simulator`.

Note: Usage points include scenarios where developers might want to provide specific tags for different types of simulations or configurations, allowing the simulator description to be customized accordingly.

Output Example: Mock up a possible appearance of the code's return value.
------The simulator user can use to test your solution------
Here are the steps to run the simulation with tag 'advanced':
1. Initialize the environment.
2. Load data set version 3.
3. Execute the model training process.
***
***
