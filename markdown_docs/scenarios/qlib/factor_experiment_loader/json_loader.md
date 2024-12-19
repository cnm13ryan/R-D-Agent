## ClassDef FactorExperimentLoaderFromDict
**FactorExperimentLoaderFromDict**: This class is designed to load factor experiment data from a dictionary format. It inherits from `FactorExperimentLoader` and implements the `load` method to parse a given dictionary containing factor details, creating instances of `FactorTask` for each entry, and then constructs a `QlibFactorExperiment` object with these tasks.

**attributes**:
· factor_dict: A dictionary where keys are factor names and values are dictionaries containing 'description', 'formulation', and 'variables' keys providing respective data about the factors.

**Code Description**: The `load` method iterates over each key-value pair in the provided dictionary. For each factor, it extracts the description, formulation, and variables from the nested dictionary associated with the factor name. Using these details, a new instance of `FactorTask` is created and added to a list named `task_l`. After all factors are processed, a `QlibFactorExperiment` object is instantiated with the list of tasks (`sub_tasks=task_l`). This experiment object encapsulates all the loaded factor tasks and is returned by the method.

**Note**: The class is typically used in scenarios where factor data is already available as a dictionary. It can be directly invoked when such data structures are present, or it can be utilized indirectly through other loaders like `FactorExperimentLoaderFromJsonFile` and `FactorExperimentLoaderFromJsonString`, which convert JSON files or strings into dictionaries before passing them to this loader.

**Output Example**: A possible return value of the `load` method could look like an instance of `QlibFactorExperiment` containing multiple `FactorTask` objects. Each task would have attributes such as:
- factor_name: 'Momentum'
- factor_description: 'Calculates momentum based on price changes over a period.'
- factor_formulation: '(close_price(t) - close_price(t-n)) / close_price(t-n)'
- variables: {'t': 1, 'n': 20}

This structure allows for the encapsulation of multiple factors within a single experiment object, facilitating their management and execution in a structured manner.
### FunctionDef load(self, factor_dict)
**load**: This function processes a dictionary containing factor data to create and return an experiment object that encapsulates multiple tasks defined by the factors.

parameters:
· factor_dict: A dictionary where each key is a factor name, and the value is another dictionary containing details about the factor such as its description, formulation, and variables.

Code Description: The function iterates over each item in the provided dictionary. For each factor, it extracts relevant information (name, description, formulation, and variables) and uses this data to instantiate a `FactorTask` object. These task objects are collected into a list. After all tasks have been created, they are passed to the constructor of `QlibFactorExperiment`, which creates an experiment object that includes these tasks as sub-tasks. The function then returns this experiment object.

Note: This function is useful for setting up experiments involving multiple factors where each factor's details are provided in a structured dictionary format. It abstracts away the process of creating individual task objects and bundling them into an experiment, making it easier to manage complex experimental setups.

Output Example: If the input dictionary were
```
{
    "factor1": {"description": "First Factor", "formulation": "x + y", "variables": ["x", "y"]},
    "factor2": {"description": "Second Factor", "formulation": "a * b", "variables": ["a", "b"]}
}
```
The function would return an instance of `QlibFactorExperiment` that includes two sub-tasks, each corresponding to the factors defined in the dictionary.
***
## ClassDef FactorExperimentLoaderFromJsonFile
**FactorExperimentLoaderFromJsonFile**: This class is designed to load factor experiments from a JSON file. It inherits from the `FactorExperimentLoader` class and implements the `load` method, which reads a JSON file containing factor experiment configurations and converts them into a list of factors.

attributes:
· json_file_path: A Path object representing the path to the JSON file that contains the factor experiment configurations.

Code Description: The `FactorExperimentLoaderFromJsonFile` class provides a specific implementation for loading factor experiments from a JSON formatted file. The `load` method takes a single argument, `json_file_path`, which is expected to be an instance of the `Path` class pointing to the location of the JSON file. Inside the method, the file is opened in read mode and its contents are parsed using the `json.load()` function, resulting in a dictionary (`factor_dict`) that represents the factor experiment configurations. This dictionary is then passed to another loader, `FactorExperimentLoaderFromDict`, which is responsible for converting the dictionary into a list of factors according to the defined specifications.

Note: Usage points include scenarios where factor experiments are stored in JSON format and need to be loaded into an application or system for further processing or analysis. The class assumes that the JSON file structure aligns with what `FactorExperimentLoaderFromDict` expects, so proper formatting is crucial for successful loading.

Output Example: Mock up a possible appearance of the code's return value.
Assuming the JSON file contains configurations for two factors, the output might look like this:
[
    {'name': 'factor1', 'type': 'technical', 'parameters': {'window': 20}},
    {'name': 'factor2', 'type': 'fundamental', 'parameters': {'sector': 'technology'}}
]
This list of dictionaries represents the loaded factors, each with a name, type, and specific parameters as defined in the JSON file.
### FunctionDef load(self, json_file_path)
**load**: This function loads factor experiment data from a JSON file by reading the file, converting its content into a dictionary, and then using another loader to process this dictionary into a structured format suitable for experimentation.

parameters:
· json_file_path: A Path object representing the file path to the JSON file containing the factor experiment data.

Code Description: The function begins by opening the specified JSON file in read mode. It uses Python's built-in `json` module to parse the file content directly into a dictionary named `factor_dict`. This dictionary is then passed to an instance of `FactorExperimentLoaderFromDict`, which processes it further. The `FactorExperimentLoaderFromDict` class iterates over each factor entry in the dictionary, creating instances of `FactorTask` for each one based on the provided details such as description, formulation, and variables. These tasks are collected into a list, which is then used to instantiate a `QlibFactorExperiment` object. This experiment object encapsulates all the loaded factor tasks and is returned by the method.

Note: The function is designed to handle JSON files specifically formatted for factor experiments. It assumes that the JSON structure aligns with what `FactorExperimentLoaderFromDict` expects, i.e., a dictionary where each key-value pair represents a factor and its associated details.

Output Example: A possible return value of the `load` method could be an instance of `QlibFactorExperiment` containing multiple `FactorTask` objects. Each task would have attributes such as:
- factor_name: 'Momentum'
- factor_description: 'Calculates momentum based on price changes over a period.'
- factor_formulation: '(close_price(t) - close_price(t-n)) / close_price(t-n)'
- variables: {'t': 1, 'n': 20}

This structure allows for the encapsulation of multiple factors within a single experiment object, facilitating their management and execution in a structured manner.
***
## ClassDef FactorExperimentLoaderFromJsonString
**FactorExperimentLoaderFromJsonString**: This class extends FactorExperimentLoader and provides a method to load factor experiments from a JSON string representation.

attributes:
· json_string: A string containing the JSON data representing the factor experiment configuration.

Code Description: The FactorExperimentLoaderFromJsonString class inherits from the FactorExperimentLoader base class. It includes a single method, `load`, which takes a JSON formatted string as input. Inside this method, the JSON string is parsed into a Python dictionary using the `json.loads` function. This dictionary is then passed to another loader, specifically an instance of `FactorExperimentLoaderFromDict`, whose `load` method is called with the dictionary. The result from this call is returned by the `load` method of FactorExperimentLoaderFromJsonString.

Note: Usage points include scenarios where factor experiment configurations are stored as JSON strings and need to be converted into a usable format for further processing or analysis within an application. This class facilitates such conversions seamlessly, leveraging existing functionality provided by `FactorExperimentLoaderFromDict`.

Output Example: Assuming the input JSON string represents two simple factors with their respective parameters, the output might look like this:

[
    {
        'name': 'MovingAverage',
        'params': {'window': 10}
    },
    {
        'name': 'ExponentialSmoothing',
        'params': {'alpha': 0.2}
    }
]

This example illustrates a list of dictionaries, each representing a factor with its name and parameters, which is the expected output format from the `load` method.
### FunctionDef load(self, json_string)
**load**: This function loads factor experiment data from a JSON string format. It converts the JSON string into a dictionary and then delegates the loading process to another loader designed to handle dictionaries.

parameters:
· json_string: A string containing JSON-formatted data representing factor experiments. The JSON structure should include keys for each factor with nested details such as description, formulation, and variables.

Code Description: The function begins by parsing the provided JSON string into a Python dictionary using `json.loads()`. This dictionary is then passed to an instance of `FactorExperimentLoaderFromDict`, which processes it further. The `FactorExperimentLoaderFromDict` class iterates over each factor entry in the dictionary, creating instances of `FactorTask` for each one based on the provided details (description, formulation, and variables). These tasks are collected into a list, which is used to instantiate a `QlibFactorExperiment` object. This experiment object encapsulates all the loaded factors and is returned by the function.

Note: The function is particularly useful when factor data is available in JSON string format, allowing for easy conversion and loading of this data into a structured experiment object. It serves as an intermediary step between raw JSON strings and the internal representation used within the system.

Output Example: A possible return value of the `load` method could be an instance of `QlibFactorExperiment` containing multiple `FactorTask` objects. Each task would have attributes such as:
- factor_name: 'Momentum'
- factor_description: 'Calculates momentum based on price changes over a period.'
- factor_formulation: '(close_price(t) - close_price(t-n)) / close_price(t-n)'
- variables: {'t': 1, 'n': 20}

This structure allows for the encapsulation of multiple factors within a single experiment object, facilitating their management and execution in a structured manner.
***
## ClassDef FactorTestCaseLoaderFromJsonFile
**FactorTestCaseLoaderFromJsonFile**: This class is designed to load test cases for factor experiments from a JSON file. It reads the JSON data, constructs `FactorTask` objects based on the information provided, initializes a `FactorFBWorkspace` for each task, injects ground truth code into these workspaces, and finally compiles all this information into a collection of `TestCase` objects.

**attributes**:
· json_file_path: A Path object representing the file path to the JSON file containing factor experiment data.

**Code Description**: The class contains one method, `load`, which takes a single parameter, `json_file_path`. This method opens and reads the specified JSON file. It expects the JSON file to contain a dictionary where each key-value pair represents a factor name and its associated data (description, formulation, variables, and ground truth code). For each factor in the JSON file, it creates a `FactorTask` object that encapsulates all relevant information about the factor. A `FactorFBWorkspace` is then instantiated for this task, which serves as an environment where the factor can be tested or evaluated. The ground truth code for the factor is injected into this workspace using the `inject_code` method. Each combination of a `FactorTask` and its corresponding `FactorFBWorkspace` (now containing the injected code) is wrapped in a `TestCase` object, which is then added to a list within a `TestCases` collection. This collection of test cases is returned by the method.

**Note**: The JSON file must be structured correctly with each factor having keys for "description", "formulation", "variables", and "gt_code". The method assumes that the ground truth code provided in the JSON file can be directly injected into a Python environment as a string under the key "factor.py".

**Output Example**: If the JSON file contains data for two factors, FactorA and FactorB, the output might look like this:

TestCases(
    test_case_l=[
        TestCase(
            task=FactorTask(factor_name='FactorA', factor_description='Description of Factor A', factor_formulation='Formulation of Factor A', variables=['var1', 'var2']),
            gt=FactorFBWorkspace(task=..., raise_exception=False)  # This workspace contains the injected ground truth code for FactorA
        ),
        TestCase(
            task=FactorTask(factor_name='FactorB', factor_description='Description of Factor B', factor_formulation='Formulation of Factor B', variables=['var3']),
            gt=FactorFBWorkspace(task=..., raise_exception=False)  # This workspace contains the injected ground truth code for FactorB
        )
    ]
)

This output represents a structured collection of test cases, each ready to be executed or evaluated in their respective workspaces.
### FunctionDef load(self, json_file_path)
**load**: This function loads test cases from a JSON file for factor experiments. It reads the JSON file, parses it into a dictionary, and then constructs `TestCases` objects based on the data provided.

**parameters**:
· json_file_path: A Path object representing the path to the JSON file containing the factor experiment data.

**Code Description**: The function begins by opening the specified JSON file in read mode. It uses the `json.load()` method to parse the contents of the file into a Python dictionary named `factor_dict`. Each key-value pair in this dictionary corresponds to a factor name and its associated data, including a description, formulation, variables, and ground truth code.

The function then initializes an empty `TestCases` object. For each factor in `factor_dict`, it creates a `FactorTask` object using the extracted information such as factor name, description, formulation, and variables. A `FactorFBWorkspace` object is instantiated with this task, where `raise_exception=False` indicates that exceptions should not be raised if an error occurs during workspace operations.

The ground truth code for each factor is injected into the `FactorFBWorkspace` using the `inject_code()` method, which takes a dictionary mapping file names to their respective code content. In this case, the key "factor.py" maps to the ground truth code provided in the JSON data.

A `TestCase` object is then created with the task and workspace objects as arguments, and it is appended to the list of test cases within the `TestCases` object.

Finally, the function returns the populated `TestCases` object containing all the constructed test cases for the factors defined in the JSON file.

**Note**: Ensure that the JSON file adheres to the expected structure with keys such as "description", "formulation", "variables", and "gt_code" under each factor name. The path provided should be correct and accessible.

**Output Example**: 
A possible return value of this function could look like:
```
TestCases(test_case_l=[
    TestCase(
        task=FactorTask(factor_name='factor1', factor_description='Description for factor 1', factor_formulation='Formulation for factor 1', variables=['var1', 'var2']),
        gt=FactorFBWorkspace(task=..., raise_exception=False)
    ),
    TestCase(
        task=FactorTask(factor_name='factor2', factor_description='Description for factor 2', factor_formulation='Formulation for factor 2', variables=['var3', 'var4']),
        gt=FactorFBWorkspace(task=..., raise_exception=False)
    )
])
```
Each `TestCase` object contains a `FactorTask` and a `FactorFBWorkspace`, representing the setup for testing each factor defined in the JSON file.
***
