## ClassDef FactorEvaluator
# Project Documentation

## Overview

This document provides a comprehensive guide to understanding, setting up, and using our project. It is designed for both developers and beginners who wish to contribute to or utilize this software.

## Table of Contents

1. [System Requirements](#system-requirements)
2. [Installation](#installation)
3. [Configuration](#configuration)
4. [Usage](#usage)
5. [API Documentation](#api-documentation)
6. [Contributing](#contributing)
7. [License](#license)

## System Requirements

Before you begin, ensure your system meets the following requirements:

- **Operating System**: Windows 10+, macOS Mojave+, Linux (Ubuntu 18.04+ recommended)
- **Memory**: At least 2 GB of RAM
- **Storage**: At least 500 MB of free disk space
- **Software**:
    - Python 3.6 or higher
    - Git

## Installation

### Step-by-Step Guide

1. **Clone the Repository**

   Open your terminal and run:

   ```bash
   git clone https://github.com/your-repo-url/project-name.git
   cd project-name
   ```

2. **Set Up a Virtual Environment**

   It's recommended to use a virtual environment for Python projects to manage dependencies.

   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows use `venv\Scripts\activate`
   ```

3. **Install Dependencies**

   Install the required packages using pip:

   ```bash
   pip install -r requirements.txt
   ```

4. **Run the Application**

   Start the application with:

   ```bash
   python main.py
   ```

## Configuration

Configuration settings are managed through a configuration file, typically named `config.ini`. Below is an example of what this file might contain:

```ini
[database]
host = localhost
port = 5432
user = your_username
password = your_password
name = project_db

[server]
host = 0.0.0.0
port = 8080
```

Modify the `config.ini` file to suit your environment and preferences.

## Usage

### Basic Commands

- **Start the Server**

  ```bash
  python main.py start
  ```

- **Stop the Server**

  ```bash
  python main.py stop
  ```

- **Restart the Server**

  ```bash
  python main.py restart
  ```

### Advanced Features

For advanced features, refer to the [API Documentation](#api-documentation) section.

## API Documentation

The project includes a RESTful API for interacting with its core functionalities. Below are some of the endpoints available:

- **GET /data**

  Retrieve data from the database.

- **POST /submit**

  Submit new data entries.

For detailed information on each endpoint, including request and response formats, see our [API Reference](api-reference.md).

## Contributing

We welcome contributions to improve this project. To contribute, follow these steps:

1. Fork the repository.
2. Create a new branch for your feature or bug fix.
3. Make your changes and commit them with descriptive messages.
4. Push your changes to your forked repository.
5. Open a pull request against the main branch of the original repository.

Please ensure that your code adheres to our coding standards and guidelines.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

Thank you for choosing this project! If you have any questions or need further assistance, feel free to reach out to us via email at support@projectname.com.
### FunctionDef __init__(self, scen)
**__init__**: Initializes an instance of the FactorEvaluator class.
parameters:
· scen: An optional parameter representing the scenario or context for which the evaluator is being initialized. This could be any data type that defines the specific conditions or settings relevant to the evaluation process.

Code Description: The __init__ method is a special method in Python classes, known as a constructor. It is automatically called when a new instance of the class is created. In this case, it initializes an object of the FactorEvaluator class with an optional parameter 'scen'. The value passed to 'scen' during the creation of a FactorEvaluator object is stored in the instance variable self.scen. This allows each instance of FactorEvaluator to potentially have its own scenario or context associated with it.

Note: Usage points include creating an instance of FactorEvaluator without specifying a scenario (in which case scen will be None by default) or providing a specific scenario at the time of object creation. The stored 'scen' can then be used within other methods of the FactorEvaluator class to tailor its behavior according to the specified scenario.
***
### FunctionDef evaluate(self, target_task, implementation, gt_implementation)
**evaluate**: This function serves as a placeholder for evaluating an implementation against a ground truth implementation for a specific task. It is designed to be overridden by subclasses, where actual evaluation logic will be implemented.

**parameters**:
· target_task: An instance of Task representing the task that needs to be evaluated.
· implementation: A Workspace object containing the code or model to be evaluated.
· gt_implementation: A Workspace object containing the ground truth code or model against which the implementation is compared.
· **kwargs: Additional keyword arguments that can be passed to the function, allowing for flexibility in how the evaluation is conducted.

**Code Description**: The function currently raises a NotImplementedError, indicating that it must be implemented by any subclass. This design pattern is common in object-oriented programming where a base class defines an interface (or abstract method) that must be implemented by derived classes. In this context, the `evaluate` method is intended to provide a standardized way of assessing how well a given implementation meets the requirements or standards set by the ground truth implementation for a particular task.

The function is expected to return a tuple consisting of two elements:
- A string providing a textual description of the evaluation result.
- An object representing a comparable metric, which could be a boolean, integer, float, etc. This metric can be used programmatically to compare different implementations or to automate decision-making processes based on the evaluation results. If the evaluator only provides a text-based result without any quantitative metrics, it should return None for this element.

**Note**: Usage points include ensuring that subclasses of FactorEvaluator implement the `evaluate` method according to their specific needs and requirements. Developers should also be aware that the function is designed to work with Task and Workspace objects, so these must be properly defined and passed when calling the evaluate method. Additionally, while **kwargs allows for flexibility, it is important to document any additional parameters expected by a subclass implementation of this method to maintain clarity and ease of use.
***
### FunctionDef _get_df(self, gt_implementation, implementation)
**_get_df**: This function retrieves dataframes from two Workspace objects: one representing a ground truth implementation (gt_implementation) and another representing an actual implementation (implementation). It ensures that both dataframes are properly formatted as pandas DataFrames, with Series being converted to single-column DataFrames and indices sorted.

parameters:
· gt_implementation: A Workspace object containing the ground truth implementation. This parameter can be None if no ground truth is available.
· implementation: A Workspace object containing the actual implementation to be evaluated.

Code Description: The function starts by checking if a ground truth implementation (gt_implementation) is provided. If it is, it executes this workspace and retrieves the resulting dataframe. If the retrieved dataframe is a pandas Series, it converts it into a DataFrame with a single column named "gt_factor". If the dataframe is already a DataFrame, it sorts its index for consistency.

If no ground truth implementation is provided (i.e., gt_implementation is None), the function sets gt_df to None.

Next, the function executes the actual implementation workspace and retrieves its resulting dataframe. Similar to the ground truth dataframe, if this dataframe is a pandas Series, it converts it into a DataFrame with a single column named "source_factor". If the dataframe is already a DataFrame, it sorts its index for consistency.

Finally, the function returns both dataframes (gt_df and gen_df) for further evaluation by other functions.

Note: This function is crucial for preparing dataframes from Workspace objects before they can be evaluated against each other. It ensures that all dataframes are in a consistent format, making it easier to compare them accurately.

Output Example: The function returns a tuple containing two pandas DataFrames or None and a pandas DataFrame. For example:
(None, DataFrame({'source_factor': [1, 2, 3]}))
or
(DataFrame({'gt_factor': [4, 5, 6]}), DataFrame({'source_factor': [7, 8, 9]}))
***
### FunctionDef __str__(self)
**__str__**: This method returns a string representation of the class name associated with an instance of the FactorEvaluator class.
parameters:
· No parameters: The __str__ method does not accept any parameters. It is called automatically when you use the print() function or str() on an object.

Code Description: Detailed analysis and description.
The __str__ method in Python is a special method used to define a human-readable string representation of an object. In this specific implementation, the method returns the name of the class (FactorEvaluator) as a string. This is achieved by accessing the __class__.__name__ attribute of the instance, which holds the name of the class type of the object.

Note: Usage points.
This method is particularly useful for debugging and logging purposes, as it allows developers to easily identify the type of an object when printed or logged. It can also be used in user interfaces where a simple description of the object's type might be displayed.

Output Example: Mock up a possible appearance of the code's return value.
If you have an instance of FactorEvaluator named evaluator and you execute print(evaluator), the output will be:
FactorEvaluator

This output indicates that the variable evaluator is an instance of the class FactorEvaluator.
***
## ClassDef FactorCodeEvaluator
**FactorCodeEvaluator**: This class extends FactorEvaluator and provides a method to evaluate code implementations based on specific tasks and feedback. It generates an evaluation response by interacting with an API backend, ensuring that the generated prompts do not exceed token limits.

attributes:
· target_task: An instance of FactorTask representing the task for which the implementation is being evaluated.
· implementation: A Workspace object containing the code to be evaluated.
· execution_feedback: A string providing feedback on the execution of the implementation.
· value_feedback: An optional string providing additional feedback on the value or quality of the implementation. Defaults to an empty string.
· gt_implementation: An optional Workspace object representing a ground truth implementation for comparison. Defaults to None.

Code Description: The evaluate method constructs a system prompt using a template from evaluate_prompts, incorporating scenario details if available. It then creates a user prompt that includes task information, the code to be evaluated, execution and value feedback, and optionally, a ground truth code. To ensure the combined prompts do not exceed the token limit defined in LLM_SETTINGS.chat_token_limit, it iteratively halves the execution_feedback string until the total token count is within limits. The method then sends these prompts to an API backend for chat completion, which returns a critic response. This response and None are returned as a tuple.

Note: Usage points include providing detailed feedback on code implementations, especially in scenarios where ground truth implementations are available for comparison. Developers can use this class to integrate automated code evaluation into their workflows, ensuring that code meets specified criteria and standards.

Output Example: ('The provided implementation successfully executes the task with minor performance issues. The logic is clear but could be optimized for speed.', None)
### FunctionDef evaluate(self, target_task, implementation, execution_feedback, value_feedback, gt_implementation)
**evaluate**: This function evaluates a given implementation of a task against specified feedback criteria using an AI-driven approach. It generates a system prompt based on the task information and scenario description, constructs a user prompt incorporating the code, execution feedback, value feedback, and optionally a ground truth implementation, and then uses these prompts to generate a critical response from an API backend.

**parameters**:
· target_task: An instance of FactorTask representing the task for which the evaluation is being performed.
· implementation: A Workspace object containing the code that needs to be evaluated.
· execution_feedback: A string providing feedback on the execution of the code, such as errors or performance issues.
· value_feedback: An optional string offering additional feedback on the value or quality aspects of the code. Defaults to an empty string if not provided.
· gt_implementation: An optional Workspace object representing the ground truth implementation against which the given implementation can be compared. Defaults to None if no comparison is needed.
· **kwargs: Additional keyword arguments that can be passed to the function, though they are not utilized within this method.

**Code Description**: The function starts by retrieving task information from the target_task parameter and extracting the code from the implementation parameter. It then constructs a system prompt using a template defined in evaluate_prompts["evaluator_code_feedback_v1_system"], incorporating a scenario description if available. For constructing the user prompt, it uses another template from evaluate_prompts["evaluator_code_feedback_v1_user"]. This user prompt includes details such as factor information, code, execution feedback, value feedback, and optionally ground truth code.

The function ensures that the combined length of system and user prompts does not exceed a predefined token limit (LLM_SETTINGS.chat_token_limit) by iteratively halving the execution feedback if necessary. Once an acceptable prompt length is achieved, it sends these prompts to an API backend for generating a chat completion, which serves as the critical response to the provided code implementation.

**Note**: The function assumes that certain external components such as evaluate_prompts, Environment, StrictUndefined, APIBackend, and LLM_SETTINGS are properly defined elsewhere in the project. Developers should ensure that these dependencies are correctly set up before using this function.

**Output Example**: A possible return value of the function could be a tuple where the first element is a string containing the critical response from the AI backend, and the second element is None:
('The provided code successfully executes without errors but can be optimized for performance by reducing unnecessary computations.', None)
***
## ClassDef FactorInfEvaluator
**FactorInfEvaluator**: This class evaluates whether a generated dataframe contains any infinite values (positive or negative infinity). It inherits from the `FactorEvaluator` class and implements the `evaluate` method to perform this check.

attributes:
· implementation: An instance of `Workspace` representing the user's implementation.
· gt_implementation: An instance of `Workspace` representing the ground truth implementation.

Code Description: The `evaluate` method in `FactorInfEvaluator` first retrieves dataframes from both the user's implementation and the ground truth implementation using the `_get_df` method inherited from `FactorEvaluator`. It then checks if the generated dataframe (`gen_df`) is `None`, returning a message indicating an issue with the implementation if so. If `gen_df` is not `None`, it counts the number of infinite values in the dataframe by checking for occurrences of positive and negative infinity using the `isin` method combined with `sum`. If no infinite values are found, it returns a success message; otherwise, it returns a failure message indicating the count of infinite values.

Note: This evaluator is crucial for ensuring data integrity, as infinite values can lead to errors in subsequent computations or analyses. It should be used when validating factor implementations that generate numerical data.

Output Example: 
The source dataframe has 3 infinite values. Please check the implementation.
False

This output indicates that there are three infinite values in the generated dataframe, and the evaluation result is `False`, signifying a failure in this particular check.
### FunctionDef evaluate(self, implementation, gt_implementation)
**evaluate**: This function evaluates an implementation by checking if it contains any infinite values within its generated dataframe compared to a ground truth implementation.

parameters:
· implementation: A Workspace object containing the actual implementation to be evaluated.
· gt_implementation: A Workspace object containing the ground truth implementation for comparison.

Code Description: The evaluate function begins by calling the _get_df method, which retrieves and formats dataframes from both the provided implementation and ground truth implementation. It checks if the generated dataframe (gen_df) is None, indicating an issue with the implementation's execution or output format. If gen_df is None, the function returns a message prompting the user to check the implementation along with a False boolean value.

If gen_df is not None, the function proceeds to count the number of infinite values in gen_df using the pandas method `isin` combined with `sum`. This process checks for both positive and negative infinity within the dataframe. If no infinite values are found (INF_count equals 0), it returns a message stating that there are no infinite values in the source dataframe along with a True boolean value.

If any infinite values are detected, the function returns a message indicating the number of infinite values found in the source dataframe and prompts the user to check the implementation, accompanied by a False boolean value. This feedback helps developers identify potential issues within their implementations that may lead to numerical instability or errors during execution.

Note: The evaluate function is designed to ensure data integrity and quality by identifying problematic infinite values in generated dataframes, which can be critical for accurate analysis and modeling.

Output Example: 
("The source dataframe has 2 infinite values. Please check the implementation.", False)
or
("The source dataframe does not have any infinite values.", True)
***
## ClassDef FactorSingleColumnEvaluator
**FactorSingleColumnEvaluator**: This class evaluates whether a given implementation generates a dataframe with exactly one column, which is essential for certain types of factor evaluations.

attributes:
· implementation: An instance of Workspace representing the user's implementation to be evaluated.
· gt_implementation: An instance of Workspace representing the ground truth or correct implementation against which the user's implementation will be compared.

Code Description: The FactorSingleColumnEvaluator class inherits from FactorEvaluator and overrides its evaluate method. This method first retrieves the dataframe generated by the user's implementation using the _get_df method inherited from FactorEvaluator. It checks if the retrieved dataframe is None, indicating an issue with the implementation, and returns a corresponding message along with False as the evaluation result. If the dataframe is not None, it then checks the number of columns in the dataframe. If there is only one column, it confirms that this meets the criteria and returns a success message along with True. If there are more than one column, it indicates an error since the task requires a single-column output and returns a failure message along with False.

Note: This evaluator specifically checks for the number of columns in the dataframe generated by the implementation. It is crucial that the implementation adheres to this requirement as having multiple columns can lead to incorrect evaluations or downstream processing issues.

Output Example: 
The source dataframe has only one column which is correct., True
or
The source dataframe has more than one column. Please check the implementation. We only evaluate the first column., False
### FunctionDef evaluate(self, implementation, gt_implementation)
**evaluate**: This function evaluates whether a given implementation produces a DataFrame with a single column by comparing it against a ground truth implementation. It uses the `_get_df` method to extract DataFrames from both implementations, then checks if the generated DataFrame has exactly one column.

parameters:
· implementation: A Workspace object containing the actual implementation to be evaluated.
· gt_implementation: A Workspace object containing the ground truth implementation for comparison.

Code Description: The function begins by calling `_get_df`, which retrieves and formats DataFrames from both the provided implementations. It then checks if the generated DataFrame (`gen_df`) is None, indicating an issue with the implementation. If `gen_df` is not None, it proceeds to check the number of columns in `gen_df`. If there is only one column, the function returns a success message along with True, signifying that the evaluation criteria are met. Otherwise, it returns a failure message and False, indicating that the generated DataFrame has more than one column.

Note: This function is designed to ensure that the implementation produces a single-column DataFrame, which might be necessary for specific types of evaluations or data processing tasks. It assumes that the `_get_df` method correctly handles the conversion and formatting of DataFrames from Workspace objects.

Output Example: The function returns a tuple containing a string message and a boolean value. For example:
("The source dataframe has only one column which is correct.", True)
or
("The source dataframe has more than one column. Please check the implementation. We only evaluate the first column.", False)
***
## ClassDef FactorOutputFormatEvaluator
**FactorOutputFormatEvaluator**: This class evaluates whether the output format of a generated dataframe matches the expected format. It inherits from the `FactorEvaluator` class and implements the `evaluate` method to perform this check.

attributes:
· implementation: An instance of the Workspace class representing the user's implementation.
· gt_implementation: An instance of the Workspace class representing the ground truth implementation for comparison.

Code Description: The `evaluate` method in the `FactorOutputFormatEvaluator` class is designed to assess the output format of a dataframe generated by the user's implementation against a ground truth dataframe. It retrieves dataframes from both implementations, checks if the generated dataframe is not None, and then proceeds with an evaluation process involving an API call.

The method first fetches the dataframes using the `_get_df` method inherited from `FactorEvaluator`. If the generated dataframe (`gen_df`) is None, it skips the evaluation and returns a message indicating that the source dataframe is None along with a False boolean value. Otherwise, it generates a string representation of the dataframe's information.

A system prompt is constructed using an environment template, which includes a scenario description if available. The method then attempts to evaluate the output format by sending a request to an API backend up to three times in case of errors such as `KeyError` or `json.JSONDecodeError`. If successful, it processes the JSON response to extract feedback and a decision on whether the output format is correct.

Note: This class assumes the existence of certain external components like `APIBackend`, `evaluate_prompts`, and methods within the `FactorEvaluator` class. It also relies on the proper configuration and availability of these components for successful execution.

Output Example: ("The user is currently working on a feature related task.\nThe output dataframe info is:\n<class 'pandas.core.frame.DataFrame'>\nRangeIndex: 100 entries, 2023-01-01 to 2023-04-10\nData columns (total 5 columns):\n #   Column      Non-Null Count  Dtype  \n---  ------      --------------  -----  \n 0   datetime    100 non-null    object \n 1   instrument  100 non-null    int64  \n 2   factor1     100 non-null    float64\n 3   factor2     100 non-null    float64\n 4   factor3     100 non-null    float64\ndtypes: float64(3), int64(1), object(1)\nmemory usage: 4.2+ KB", True)
### FunctionDef evaluate(self, implementation, gt_implementation)
**evaluate**: This function evaluates whether the output format of a given implementation matches the expected format based on a ground truth implementation. It uses dataframes extracted from Workspace objects to perform this evaluation.

parameters:
· implementation: A Workspace object containing the actual implementation to be evaluated.
· gt_implementation: A Workspace object containing the ground truth implementation against which the actual implementation will be compared.

Code Description: The function begins by retrieving two dataframes, one from the ground truth implementation and another from the actual implementation, using the _get_df method. If the dataframe from the actual implementation is None, it returns a message indicating that evaluation cannot proceed and False as the decision result.

The function then generates a string representation of the information about the generated dataframe (gen_df) using pandas' info() method. This information includes details such as column names, non-null counts, data types, memory usage, etc., which are crucial for evaluating the output format.

A system prompt is constructed using an environment template and scenario descriptions related to a feature task from the implementation's target task. If no scenario description is available, it defaults to "No scenario description."

The function attempts up to three times to evaluate the output format by sending the generated dataframe information and system prompt to an API backend for processing. The API backend returns a JSON response containing feedback on the output format and a decision (True or False) indicating whether the output format is correct.

If the API response contains missing keys or if there are issues with decoding the JSON, the function retries up to three times before raising a KeyError.

Note: This function is essential for automated evaluation of dataframes generated by different implementations against predefined standards. It leverages external APIs and scenario descriptions to provide detailed feedback on the output format correctness.

Output Example: The function returns a tuple containing a string with feedback about the output format and a boolean indicating whether the output format is correct. For example:
("The output dataframe has the expected columns but incorrect data types.", False)
or
("The output dataframe matches the required format.", True)
***
## ClassDef FactorDatetimeDailyEvaluator
**FactorDatetimeDailyEvaluator**: This class evaluates whether a generated dataframe has a daily datetime index format. It inherits from the `FactorEvaluator` class and implements the `evaluate` method to perform this check.

attributes:
· implementation: An instance of Workspace representing the user's implementation.
· gt_implementation: An instance of Workspace representing the ground truth implementation for comparison.

Code Description: The `FactorDatetimeDailyEvaluator` class is designed to verify if the dataframe produced by a given implementation has a daily datetime index. It first retrieves the generated dataframe using the `_get_df` method inherited from its parent class, `FactorEvaluator`. If the retrieved dataframe is None, it returns a message indicating that the evaluation of the datetime format should be skipped.

The evaluator then checks whether the dataframe's index contains a "datetime" level. If this level does not exist, it returns an error message prompting the user to check their implementation. Next, it attempts to convert the "datetime" index values to pandas datetime objects. If this conversion fails due to incorrect format (e.g., regular strings or other objects), it returns an error message along with a preview of the dataframe's head.

If the datetime index is correctly formatted, the evaluator calculates the time differences between consecutive entries in the index and checks for any occurrences of one-minute intervals. The presence of such intervals indicates that the dataframe does not have a daily frequency, leading to a failure message. If no one-minute intervals are found, it confirms that the dataframe has a daily format.

Note: This class is typically used as part of a larger evaluation process, where multiple aspects of the generated dataframe are checked against ground truth data. It is crucial for tasks requiring time-series analysis with daily frequency.

Output Example: 
"The source dataframe is None. Skip the evaluation of the datetime format.", False
"The source dataframe does not have a datetime index. Please check the implementation.", False
"The source dataframe has a datetime index but it is not in the correct format (maybe a regular string or other objects). Please check the implementation.\n The head of the output dataframe is: \n[DataFrame Head]", False
"The generated dataframe is not daily. The implementation is definitely wrong. Please check the implementation.", False
"The generated dataframe is daily.", True
### FunctionDef evaluate(self, implementation, gt_implementation)
**evaluate**: This function evaluates whether a generated dataframe from an implementation has a daily datetime index format by comparing it against a ground truth implementation's dataframe.

parameters:
· implementation: A Workspace object containing the actual implementation to be evaluated.
· gt_implementation: A Workspace object containing the ground truth implementation. This parameter can be None if no ground truth is available.

Code Description: The function begins by calling _get_df, which retrieves and prepares dataframes from both the provided implementations (ground truth and actual). If the generated dataframe (gen_df) is None, it returns a message indicating that evaluation cannot proceed due to the missing source dataframe. 

Next, it checks if the 'datetime' index exists in gen_df's index names. If not, it returns an error message suggesting that the implementation needs to be checked for proper datetime indexing.

The function then attempts to convert the 'datetime' index values of gen_df into pandas datetime objects. If this conversion fails due to incorrect format (e.g., regular strings or other non-datetime objects), it catches the exception and returns a detailed error message, including the head of the output dataframe for further inspection.

After successful conversion, the function calculates the unique differences between consecutive datetime values in the index. It checks if any of these differences are equal to one minute (pd.Timedelta(minutes=1)), which would indicate that the data is not daily but rather at a higher frequency (e.g., minutely). If such a difference is found, it returns an error message stating that the implementation is incorrect.

If none of the above conditions are met, indicating that the dataframe has a proper daily datetime index, the function returns a success message confirming this.

Note: This function is essential for validating the temporal resolution of dataframes generated by different implementations, ensuring they meet the expected daily frequency requirement.

Output Example: The function could return either:
"The source dataframe is None. Skip the evaluation of the datetime format.", False
or
"The source dataframe does not have a datetime index. Please check the implementation.", False
or
"The source dataframe has a datetime index but it is not in the correct format (maybe a regular string or other objects). Please check the implementation.\n The head of the output dataframe is: \n{gen_df.head()}", False
or
"The generated dataframe is not daily. The implementation is definitely wrong. Please check the implementation.", False
or
"The generated dataframe is daily.", True
***
## ClassDef FactorRowCountEvaluator
**FactorRowCountEvaluator**: This class evaluates the row count ratio between a generated dataframe and a ground truth dataframe. It inherits from the `FactorEvaluator` class and implements the `evaluate` method to compare the number of rows in both dataframes, returning a descriptive message and the calculated ratio.

attributes:
· implementation: An instance of Workspace representing the user's implementation.
· gt_implementation: An instance of Workspace representing the ground truth implementation.

Code Description: The `FactorRowCountEvaluator` class extends `FactorEvaluator`. It overrides the `evaluate` method to perform row count comparison between two dataframes. First, it retrieves the dataframes from the provided `Workspace` instances using the `_get_df` method inherited from `FactorEvaluator`. If the generated dataframe (`gen_df`) is None, it returns a message indicating that the source dataframe is missing and a boolean value False. Otherwise, it calculates the ratio of the minimum row count to the maximum row count between the two dataframes. The method then constructs a feedback string based on whether this ratio is less than 0.99, suggesting a discrepancy in row counts if true. It returns both the feedback message and the calculated ratio.

Note: This evaluator is useful for ensuring that the number of rows in the generated dataframe aligns with expectations set by the ground truth dataframe. Discrepancies in row counts could indicate issues such as data loss or incorrect filtering during processing.

Output Example: 
("The ratio of rows count in the source dataframe to the ground truth dataframe is 0.85. Please verify the implementation.", 0.85)
This output indicates that the generated dataframe has only 85% of the rows present in the ground truth dataframe, suggesting a potential issue with data completeness or processing accuracy.
### FunctionDef evaluate(self, implementation, gt_implementation)
**evaluate**: This function assesses the row count of a generated dataframe against a ground truth dataframe by calculating their ratio. It provides feedback if the ratio is below 0.99, indicating potential discrepancies between the two datasets.

parameters:
· implementation: A Workspace object containing the actual implementation to be evaluated.
· gt_implementation: A Workspace object containing the ground truth implementation for comparison.

Code Description: The function begins by invoking the _get_df method with both the provided implementations (gt_implementation and implementation) to retrieve their respective dataframes. If the generated dataframe (gen_df) is None, it returns a message indicating that the source dataframe is missing along with a False value to signify an error or issue in the implementation.

If gen_df is not None, the function calculates the ratio of the minimum row count between gen_df and gt_df to their maximum row count. This ratio helps determine how closely aligned the sizes of the two dataframes are. If this ratio is less than or equal to 0.99, it suggests that there might be a significant difference in the number of rows between the generated dataframe and the ground truth dataframe, prompting a verification message. Otherwise, no additional feedback is provided.

Note: This function is essential for validating the consistency and accuracy of data generation processes by comparing their output against expected results (ground truth). It helps identify potential issues related to data completeness or correctness early in the development process.

Output Example: The function returns a tuple containing a string message and a float value representing the row count ratio. For instance:
("The ratio of rows count in the source dataframe to the ground truth dataframe is 0.85. Please verify the implementation.", 0.85)
or
("", 1.0)
***
## ClassDef FactorIndexEvaluator
**FactorIndexEvaluator**: This class evaluates the similarity of indices between a generated dataframe and a ground truth dataframe. It inherits from the `FactorEvaluator` class and implements the `evaluate` method to compare the indices, returning both a textual description and a numerical similarity score.

attributes:
· implementation: An instance of `Workspace` representing the generated data.
· gt_implementation: An instance of `Workspace` representing the ground truth data.

Code Description: The `FactorIndexEvaluator` class extends `FactorEvaluator`. Its primary function is to assess how similar the indices of two dataframes are. It first retrieves the dataframes from the provided `Workspace` instances using a helper method `_get_df`. If the generated dataframe (`gen_df`) is None, it returns an error message indicating that the source dataframe is missing and a boolean value False.

The class then converts the indices of both dataframes into sets (`gen_index_set` and `gt_index_set`). It calculates the similarity between these two sets using the Jaccard index formula: the size of the intersection divided by the size of the union. This similarity score indicates how much the indices overlap, with 1 being a perfect match.

If the calculated similarity is less than or equal to 0.99, it returns a message indicating that there are differences between the source and ground truth dataframes along with their similarity percentage. Otherwise, it returns an empty string for the textual description, signifying high similarity.

Note: This class is typically used in scenarios where the alignment of indices between two datasets is crucial, such as time series analysis or feature matching tasks. It helps ensure that the generated data aligns correctly with the expected ground truth data.

Output Example: 
The source dataframe and the ground truth dataframe have different index with a similarity of 95.00%. The similarity is calculated by the number of shared indices divided by the union indices. Please check the implementation.
0.95

In this example, the similarity score between the indices of the generated and ground truth dataframes is 0.95, indicating that while they are quite similar, there are some discrepancies in their indices that may require further investigation or adjustment in the implementation.
### FunctionDef evaluate(self, implementation, gt_implementation)
**evaluate**: This function assesses the similarity between the indices of two dataframes derived from Workspace objects representing a ground truth implementation and an actual implementation. It calculates the Jaccard index, which measures the similarity between the sets of indices from both dataframes.

parameters:
· implementation: A Workspace object containing the actual implementation to be evaluated.
· gt_implementation: A Workspace object containing the ground truth implementation against which the actual implementation is compared.

Code Description: The function begins by calling _get_df with the provided Workspace objects for the ground truth and actual implementations. This retrieves two dataframes, one from each Workspace. If the dataframe derived from the actual implementation (gen_df) is None, it returns a message indicating that the source dataframe is None and suggests checking the implementation.

If gen_df is not None, the function proceeds to convert the indices of both dataframes into sets: gen_index_set for the generated dataframe and gt_index_set for the ground truth dataframe. It then calculates the similarity between these two sets using the Jaccard index formula, which is the size of the intersection divided by the size of the union of the two sets.

The function returns a tuple containing a message and the calculated similarity value. If the similarity is less than or equal to 0.99, it includes a message indicating that the indices differ significantly along with the similarity percentage. Otherwise, it returns an empty string for the message, implying high similarity between the indices.

Note: This function is essential for evaluating how closely the indices of two dataframes align, which can be crucial in scenarios where the order or presence of specific indices is significant for the correctness of the implementation.

Output Example: The function might return a tuple like this:
("The source dataframe and the ground truth dataframe have different index with a similarity of 85.00%. The similarity is calculated by the number of shared indices divided by the union indices. Please check the implementation.", 0.85)
or
("", 1.0) if the indices are identical in both dataframes.
***
## ClassDef FactorMissingValuesEvaluator
**FactorMissingValuesEvaluator**: This class evaluates whether two dataframes have the same missing values by comparing the total number of missing entries in each dataframe.

attributes:
· implementation: An instance of Workspace representing the source implementation to be evaluated.
· gt_implementation: An instance of Workspace representing the ground truth implementation for comparison.

Code Description: The FactorMissingValuesEvaluator class inherits from FactorEvaluator. It overrides the evaluate method, which is responsible for comparing the number of missing values in two dataframes derived from the provided implementations. The method first retrieves the dataframes using the _get_df method inherited from FactorEvaluator. If the source dataframe (gen_df) is None, it returns a message indicating that the implementation needs to be checked and False as the evaluation result. It then compares the total number of missing values in both dataframes by summing up NaN values across all columns and rows using the isna().sum().sum() method. If the counts match, it returns a message stating that both dataframes have the same missing values along with True. Otherwise, it provides a detailed message indicating the discrepancy in the number of missing values between the two dataframes and False as the evaluation result.

Note: This evaluator assumes that the order and structure of the dataframes are aligned for comparison purposes. It is crucial to ensure that both implementations produce dataframes with identical indices and columns for accurate evaluation.

Output Example: ("Both dataframes have the same missing values.", True)
or
("The dataframes do not have the same missing values. The source dataframe has 10 missing values, while the ground truth dataframe has 5 missing values. Please check the implementation.", False)
### FunctionDef evaluate(self, implementation, gt_implementation)
**evaluate**: This function evaluates whether two Workspace objects have dataframes with identical missing values. It uses the _get_df method to extract and prepare the dataframes from these Workspace objects for comparison.

parameters:
· implementation: A Workspace object containing the actual implementation to be evaluated.
· gt_implementation: A Workspace object containing the ground truth implementation against which the actual implementation will be compared.

Code Description: The function begins by calling the _get_df method with the provided Workspace objects. This method returns two dataframes, one from each Workspace object (gt_df for the ground truth and gen_df for the generated or actual implementation). If the gen_df is None, indicating that there was an issue retrieving the dataframe from the implementation Workspace, the function immediately returns a message suggesting to check the implementation along with False as the evaluation result.

If gen_df is not None, the function proceeds to compare the total number of missing values in both dataframes. This comparison is done by summing up all NaN (missing) values across each dataframe using the .isna().sum().sum() method. If both dataframes have the same number of missing values, it returns a message stating that both dataframes have the same missing values along with True as the evaluation result.

If the number of missing values differs between the two dataframes, the function returns a detailed message indicating the discrepancy in the count of missing values for each dataframe. This message also suggests checking the implementation to resolve any issues. The function returns False as the evaluation result in this case.

Note: This function is essential for ensuring that the actual implementation produces a dataframe with the same pattern of missing values as the ground truth, which can be crucial for validating the correctness and consistency of data processing workflows.

Output Example: The function could return one of the following tuples:
("The source dataframe is None. Please check the implementation.", False)
or
("Both dataframes have the same missing values.", True)
or
("The dataframes do not have the same missing values. The source dataframe has 5 missing values, while the ground truth dataframe has 3 missing values. Please check the implementation.", False)
***
## ClassDef FactorEqualValueRatioEvaluator
**FactorEqualValueRatioEvaluator**: This class evaluates the equality of values between two dataframes generated from different implementations. It checks how many values in the generated dataframe match those in a ground truth dataframe within a specified tolerance (1e-6). The evaluation returns both a textual description and a numerical accuracy rate.

attributes:
· implementation: An instance of Workspace representing the user's implementation.
· gt_implementation: An instance of Workspace representing the ground truth implementation.

Code Description: The `FactorEqualValueRatioEvaluator` class inherits from `FactorEvaluator`. It overrides the `evaluate` method to compare two dataframes. First, it retrieves the dataframes using the `_get_df` method inherited from `FactorEvaluator`. If the generated dataframe (`gen_df`) is None, it returns a message indicating an issue with the implementation and a score of -1.

The method then attempts to compute the number of values in `gen_df` that are close to those in `gt_df`, within a tolerance of 1e-6. This is done by subtracting one dataframe from the other, taking the absolute value, and checking if it's less than 1e-6. The result is converted to integers (0 or 1) and summed to count the number of close values. The accuracy rate is calculated as the ratio of these close values to the total number of elements in the dataframe.

If an exception occurs during this process, `gen_df` is assigned directly to `close_values`, but this part seems incomplete and might be a placeholder or error handling that needs refinement.

The method checks if all values are equal within the tolerance. If so, it returns a message indicating this and the accuracy rate. Otherwise, it suggests checking for rounding errors or differences in calculation methods and still provides the accuracy rate.

Note: Usage points include ensuring both implementations produce dataframes with compatible shapes and indices to avoid unexpected results. The accuracy rate can be used as a quantitative measure of how closely the generated dataframe matches the ground truth.

Output Example: 
("All values in the dataframes are equal within the tolerance of 1e-6.", 0.998)
or
("Some values differ by more than the tolerance of 1e-6. Check for rounding errors or differences in the calculation methods.", 0.75)
### FunctionDef evaluate(self, implementation, gt_implementation)
**evaluate**: This function evaluates two Workspace objects by comparing their dataframes to determine how closely they match within a specified tolerance level of 1e-6.

parameters:
· implementation: A Workspace object containing the actual implementation to be evaluated.
· gt_implementation: A Workspace object containing the ground truth implementation against which the actual implementation is compared. This parameter can be None if no ground truth is available.

Code Description: The function begins by calling _get_df, a helper method that retrieves and formats dataframes from both the provided Workspace objects (implementation and gt_implementation). If the ground truth dataframe (gt_df) or the generated dataframe (gen_df) cannot be obtained, gen_df will be None. In this case, the function immediately returns an error message indicating that the source dataframe is None and a score of -1.

If both dataframes are successfully retrieved, the function proceeds to compare them element-wise. It calculates the absolute difference between corresponding elements in gen_df and gt_df, checking if these differences are less than 1e-6 (a small tolerance level). This comparison results in a boolean dataframe where True indicates that the values are close enough within the specified tolerance.

The boolean dataframe is then converted to an integer dataframe, with True values becoming 1 and False values becoming 0. The sum of all these integers gives the total number of positions where the two dataframes have equal or nearly equal values (within the tolerance). This count is divided by the total number of elements in the dataframe to compute the accuracy rate (acc_rate).

If an exception occurs during this process, gen_df is assigned directly to close_values. However, based on the current implementation, it seems that this except block might not be handling any specific exceptions and could potentially lead to unexpected behavior if gen_df is not a boolean dataframe as expected.

Finally, the function checks if all values in the close_values dataframe are True (indicating complete equality within the tolerance). If so, it returns a message stating that all values match within the tolerance along with the accuracy rate. Otherwise, it returns a message indicating that some values differ and suggests checking for rounding errors or differences in calculation methods, again accompanied by the accuracy rate.

Note: This function is designed to assess the similarity between two sets of data produced by different implementations, particularly useful in scenarios where floating-point precision might introduce minor discrepancies.

Output Example: The function could return something like:
("All values in the dataframes are equal within the tolerance of 1e-6.", 0.98)
or
("Some values differ by more than the tolerance of 1e-6. Check for rounding errors or differences in the calculation methods.", 0.75)
***
## ClassDef FactorCorrelationEvaluator
**FactorCorrelationEvaluator**: This class evaluates the correlation between a generated dataframe and a ground truth dataframe. It inherits from `FactorEvaluator` and provides functionality to assess both Pearson (IC) and Spearman (RIC) correlations, with an option for a strict threshold check.

**attributes**:
· hard_check: A boolean flag indicating whether to perform a strict check on the correlation values. If set to True, the evaluator will return a pass only if both IC and RIC are greater than 0.99.
· scen: An optional parameter inherited from `FactorEvaluator`, representing the scenario or context in which the evaluation is performed.

**Code Description**: The class initializes with a hard_check flag and optionally accepts additional arguments that are passed to its superclass constructor. The primary method, `evaluate`, takes two Workspace objects as input: one for the generated implementation (`implementation`) and another for the ground truth implementation (`gt_implementation`). It retrieves dataframes from these workspaces using the `_get_df` method inherited from `FactorEvaluator`. If the generated dataframe is None, it returns a message indicating an issue with the implementation. Otherwise, it concatenates the two dataframes along the columns, labels them as 'source' and 'gt', and calculates both Pearson and Spearman correlations grouped by datetime. The mean of these correlation values across all datetimes is computed. If `hard_check` is True, the method checks if both IC and RIC exceed 0.99; otherwise, it simply returns the calculated correlation values.

**Note**: This evaluator is particularly useful in scenarios where the consistency and similarity between generated data and ground truth data are critical, such as in financial factor analysis or similar domains requiring high precision.

**Output Example**: 
If `hard_check` is True and both IC and RIC are greater than 0.99:
"The dataframes are highly correlated. The ic is 0.995678 and the rankic is 0.994321."
True

If `hard_check` is True but either IC or RIC is less than 0.99:
"The dataframes are not sufficiently high correlated. The ic is 0.985678 and the rankic is 0.974321. Investigate the factors that might be causing the discrepancies and ensure that the logic of the factor calculation is consistent."
False

If `hard_check` is False:
"The ic is (0.985678) and the rankic is (0.974321)."
0.985678  # This would be the IC value returned as a metric for comparison purposes
### FunctionDef __init__(self, hard_check)
**__init__**: Initializes an instance of the FactorCorrelationEvaluator class, setting up a flag to determine whether strict checks should be performed.

parameters:
· hard_check: A boolean value indicating whether the evaluator should perform strict or lenient checks.
· *args: Variable length argument list passed to the superclass constructor.
· **kwargs: Arbitrary keyword arguments passed to the superclass constructor.

Code Description: The __init__ method is a special method in Python classes used for initializing new objects. This particular initializer first calls the constructor of its superclass using super().__init__(*args, **kwargs), which allows it to inherit and initialize any attributes or behaviors from the parent class. Following this, it sets an instance variable self.hard_check to the value provided by the hard_check parameter. This variable will be used within the class to control whether certain operations should adhere to stricter criteria.

Note: Usage points include ensuring that when creating an instance of FactorCorrelationEvaluator, one specifies whether strict checks are required by setting the hard_check parameter appropriately. The use of *args and **kwargs allows for flexibility in passing additional arguments to the superclass constructor if needed.
***
### FunctionDef evaluate(self, implementation, gt_implementation)
**evaluate**: This function assesses the correlation between two implementations by comparing their generated dataframes against a ground truth dataframe. It calculates both the Pearson (IC) and Spearman (RIC) correlation coefficients to determine how closely aligned the source implementation is with the ground truth.

parameters:
· implementation: A Workspace object representing the actual implementation whose output needs to be evaluated.
· gt_implementation: A Workspace object containing the ground truth implementation against which the actual implementation will be compared.

Code Description: The function begins by calling _get_df, a helper method that extracts and formats dataframes from the provided Workspace objects. It retrieves two dataframes: one for the ground truth (gt_df) and another for the generated implementation (gen_df). If gen_df is None, indicating an issue with the source dataframe, the function returns an error message along with False.

If gen_df is not None, the function concatenates gt_df and gen_df side by side into a single DataFrame named concat_df. The columns of this concatenated DataFrame are labeled "source" for the generated implementation and "gt" for the ground truth.

Next, it calculates two correlation coefficients:
- IC (Information Coefficient): The Pearson correlation coefficient between the source and ground truth data points grouped by datetime.
- RIC (Rank Information Coefficient): The Spearman rank correlation coefficient between the same data points, also grouped by datetime. Both coefficients are averaged across all available datetimes.

If hard_check is enabled in the instance of FactorCorrelationEvaluator, the function performs a strict comparison:
- If both IC and RIC exceed 0.99, it indicates a high degree of similarity between the source and ground truth implementations, and the function returns a success message along with True.
- Otherwise, it returns a failure message suggesting further investigation into potential discrepancies.

If hard_check is disabled, the function simply returns the calculated IC and RIC values without performing any threshold checks.

Note: This function is essential for evaluating the accuracy and consistency of factor calculations in financial or statistical models by comparing them against established benchmarks.

Output Example: The function could return either a tuple with a message and a boolean value when hard_check is enabled, or a string along with the IC and RIC values when hard_check is disabled. For example:
("The dataframes are highly correlated. The ic is 0.995432 and the rankic is 0.987654.", True)
or
("The ic is (0.951234) and the rankic is (0.934567).", 0.951234, 0.934567)
***
## ClassDef FactorValueEvaluator
**FactorValueEvaluator**: This class extends `FactorEvaluator` and is designed to evaluate the implementation of a factor against a ground truth implementation. It checks various aspects such as dataframe format, row count, index consistency, missing values, equal value ratio, and correlation between the generated and ground truth dataframes.

attributes:
· implementation: An instance of `Workspace` representing the user's factor implementation.
· gt_implementation: An instance of `Workspace` representing the ground truth factor implementation.
· version: An integer indicating the type of factors being evaluated (1 for qlib factors, 2 for kaggle factors).
· kwargs: Additional keyword arguments that can be passed to the evaluate method.

Code Description: The `evaluate` method initializes several result variables and then proceeds with a series of checks based on the specified version. For version 1, it ensures that the dataframe has only one column (though this check is now muted as factor tasks might generate multiple columns). It evaluates the implementation using various evaluators such as `FactorSingleColumnEvaluator`, `FactorInfEvaluator`, `FactorOutputFormatEvaluator`, and others depending on the version.

The method checks if the index of the dataframe consists of ("datetime", "instrument") and verifies the format, row count, index consistency, missing values, equal value ratio, and correlation between the generated and ground truth dataframes. Feedback strings from these evaluations are collected in a list called `conclusions`.

After all checks, it combines the feedback strings into a single string and determines a decision based on the evaluation results. If the implementation has an equal value ratio greater than 0.99 or high correlation with the ground truth, it is considered successful. Otherwise, if there are issues such as mismatched row counts, incorrect output format, or presence of infinite values, it is deemed unsuccessful.

Note: The `FactorValueEvaluator` class relies on several other evaluators to perform specific checks and provides a comprehensive evaluation of factor implementations against their ground truth counterparts.

Output Example: 
("Feedback from FactorSingleColumnEvaluator\nFeedback from FactorInfEvaluator\nFeedback from FactorOutputFormatEvaluator\n...\nFinal conclusion based on all checks", True)

In this example, the first part is a concatenated string of feedback messages from various evaluators, and the second part is a boolean indicating whether the implementation passed or failed the evaluation.
### FunctionDef evaluate(self, implementation, gt_implementation, version)
# Project Documentation

## Overview

This document provides a comprehensive guide to understanding and utilizing the [Project Name] software. It is designed for both developers who wish to integrate this project into their applications and beginners looking to explore its functionalities.

## Table of Contents

1. **Installation**
2. **Getting Started**
3. **Core Features**
4. **API Documentation**
5. **Configuration Options**
6. **Troubleshooting**
7. **Contributing**
8. **License**

---

### 1. Installation

#### Prerequisites
- Ensure you have [Prerequisite Software] installed on your system.
- Verify that your system meets the minimum requirements: [Minimum Requirements].

#### Steps to Install
1. Clone the repository:
   ```bash
   git clone https://github.com/your-repo/project-name.git
   ```
2. Navigate to the project directory:
   ```bash
   cd project-name
   ```
3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

### 2. Getting Started

#### Basic Usage
- To start using [Project Name], run the following command:
  ```bash
  python main.py
  ```
- This will launch the application with default settings.

#### Example Workflow
1. Initialize a new project.
2. Configure your settings as per the configuration options detailed in section 5.
3. Execute tasks by invoking specific commands or functions.

### 3. Core Features

- **Feature One**: Description of what this feature does and how it benefits users.
- **Feature Two**: Description of what this feature does and how it benefits users.
- **Feature Three**: Description of what this feature does and how it benefits users.

### 4. API Documentation

#### Endpoints
- **GET /api/data**
  - Retrieves data from the system.
  - Parameters:
    - `id`: Unique identifier for the data item.
  - Response: JSON object containing the requested data.

- **POST /api/data**
  - Adds new data to the system.
  - Body: JSON object with data fields.
  - Response: Confirmation message and ID of the newly created data item.

#### Authentication
API endpoints require authentication using an API key. Include your API key in the request headers as follows:
```http
Authorization: Bearer YOUR_API_KEY
```

### 5. Configuration Options

- **Configuration File**: Located at `config/settings.ini`.
- **Editable Parameters**:
  - `api_key`: Your unique API access token.
  - `log_level`: Set to `DEBUG`, `INFO`, `WARNING`, `ERROR`, or `CRITICAL`.

### 6. Troubleshooting

#### Common Issues
- **Issue One**: Description of the issue and steps to resolve it.
- **Issue Two**: Description of the issue and steps to resolve it.

#### Contact Support
For assistance, contact support at [support-email@example.com] or visit our [Support Page].

### 7. Contributing

We welcome contributions from the community! To contribute:
1. Fork the repository on GitHub.
2. Create a new branch for your feature or bug fix.
3. Make changes and commit them with descriptive messages.
4. Push to your fork and submit a pull request.

#### Code of Conduct
Please adhere to our [Code of Conduct] when contributing to this project.

### 8. License

This project is licensed under the [License Type]. For more information, see the [LICENSE](https://github.com/your-repo/project-name/blob/main/LICENSE) file in the repository.

---

Thank you for choosing [Project Name]. We hope you find it useful and easy to integrate into your projects. If you have any feedback or suggestions, please feel free to share them with us.
***
## ClassDef FactorFinalDecisionEvaluator
**FactorFinalDecisionEvaluator**: This class extends `FactorEvaluator` and is designed to evaluate a final decision based on feedback from execution, value, and code. It constructs prompts using system and user templates, interacts with an API backend for evaluation, and handles retries in case of errors.

attributes:
· target_task: An instance of `FactorTask`, representing the task being evaluated.
· execution_feedback: A string containing feedback related to the execution of the task.
· value_feedback: A string containing feedback related to the value or output of the task. If not provided, a default message is used.
· code_feedback: A string containing feedback related to the code quality or structure.

Code Description: The `evaluate` method constructs a system prompt using a template and scenario description from `target_task`. It then creates a user prompt incorporating feedback details. To ensure the prompts do not exceed a token limit, it iteratively shortens the execution feedback if necessary. The method attempts to obtain a final evaluation by sending these prompts to an API backend up to three times. If successful, it parses the JSON response for `final_decision` and `final_feedback`, converting `final_decision` to a boolean value before returning both as a tuple. In case of errors during JSON parsing or missing keys in the response, appropriate exceptions are raised.

Note: The method includes a retry mechanism with up to three attempts to handle potential issues with API responses. It also manages token limits by truncating feedback content if necessary.

Output Example: (True, "The task was executed successfully and met all value criteria.")
This example indicates that the final decision is positive (`True`), and the feedback message suggests successful execution and meeting of specified criteria.
### FunctionDef evaluate(self, target_task, execution_feedback, value_feedback, code_feedback)
**evaluate**: This function evaluates a given task based on feedback related to execution, value, and code quality. It constructs prompts using Jinja2 templates and interacts with an API backend to generate a final decision and feedback.

parameters:
· target_task: An instance of FactorTask representing the task being evaluated.
· execution_feedback: A string containing feedback about the execution of the task.
· value_feedback: A string containing feedback regarding the value or outcome of the task. If no ground truth is provided, it defaults to a message indicating that no evaluation on value is performed.
· code_feedback: A string providing feedback on the quality of the code used in the task.
· **kwargs: Additional keyword arguments that can be passed but are not explicitly handled within this function.

Code Description: The function begins by constructing a system prompt using a Jinja2 template, incorporating scenario descriptions from the target_task if available. It then prepares a user prompt with detailed information about the task and various feedback categories. If the combined length of the prompts exceeds a predefined token limit (LLM_SETTINGS.chat_token_limit), it iteratively halves the execution_feedback to fit within the limit.

The function attempts up to three times to send these prompts to an API backend, requesting a JSON response containing a final decision and feedback. It uses a seed value for randomness in case of retries with caching enabled. If successful, it parses the JSON response, converts the 'final_decision' to a boolean, and returns this along with the 'final_feedback'. In case of errors during parsing or missing keys in the API response, appropriate exceptions are raised.

Note: The function includes a retry mechanism for handling potential issues with the API response. It also handles cases where no scenario description is available by using a default message.

Output Example: (True, "The task was executed successfully and met all specified criteria.")
***
