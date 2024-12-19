## ClassDef FactorRDLoop
**FactorRDLoop**: This class extends RDLoop and is specifically designed to handle the research and development loop for financial technology factors, focusing on factor extraction and evaluation.

attributes:
· skip_loop_error: A tuple containing exceptions that should be skipped during the loop execution. In this case, it includes FactorEmptyError, indicating that if a FactorEmptyError occurs, the loop will not terminate but continue with the next iteration.
· runner: An instance of a runner object responsible for developing and extracting factors from the provided coding data.

Code Description: The FactorRDLoop class overrides the running method from its parent RDLoop class. This method is decorated with @measure_time, which likely measures the execution time of the method. Inside the running method, it uses a logger to tag the section as "ef" for evaluate and feedback. It then calls the develop method on the runner object, passing in the coding data from the prev_out dictionary. If the result (exp) is None, indicating that factor extraction failed, an error message is logged, and a FactorEmptyError is raised. Otherwise, the extracted factors are logged with the tag "runner result". The method returns the extracted factors.

Note: This class is typically instantiated in the main function of the module, either using default settings or by loading from a specified path. It can be run for a specific number of steps if step_n is provided.

Output Example: Assuming successful factor extraction, the output might look like this:
```
{
    'factor1': [0.234, 0.567, -0.123],
    'factor2': [-0.987, 0.345, 0.678]
}
```
This dictionary contains factor names as keys and lists of numerical values representing the factors for different data points or assets. If an error occurs during extraction, a FactorEmptyError will be raised with a message indicating failure in factor extraction.
### FunctionDef running(self, prev_out)
**running**: This function processes a previous output dictionary to extract factors using an internal runner mechanism. It logs the process and handles potential errors if factor extraction fails.

parameters:
· prev_out: A dictionary containing the previous outputs, specifically expecting a key "coding" which holds data necessary for factor extraction.

Code Description: The function starts by logging its operation under the tag "ef", signifying it is in an evaluation and feedback phase. It then calls the `develop` method of the `runner` object, passing the value associated with the "coding" key from the `prev_out` dictionary. If the result (`exp`) from this call is None, indicating that factor extraction has failed, the function logs an error message and raises a `FactorEmptyError`. Otherwise, it logs the successful extraction result under the tag "runner result". Finally, the extracted factors (or experiment results) are returned.

Note: This function assumes that the `runner` object has a method named `develop` which is capable of processing the provided coding data to extract factors. It also relies on an external logging mechanism and error handling class (`FactorEmptyError`) for its operations.

Output Example: Assuming successful factor extraction, the output could be a dictionary or any other structured format containing the extracted factors. For instance:
```python
{
    'factor1': [0.23, 0.45, 0.67],
    'factor2': [0.89, 0.12, 0.34]
}
```
This output represents two different factors with their corresponding values across some observations or data points.
***
## FunctionDef main(path, step_n)
**main**: Auto R&D Evolving loop for fintech factors.

parameters:
· path: Optional parameter specifying the path to load a previously saved session of FactorRDLoop. If not provided, the function initializes a new instance using default settings.
· step_n: Optional parameter indicating the number of steps to run in the model loop. If not specified, it defaults to running until completion or interruption.

Code Description: The main function serves as an entry point for initiating and managing the Auto R&D Evolving loop tailored for financial technology factors. It accepts two optional parameters: `path` and `step_n`. If a `path` is provided, the function loads an existing session of FactorRDLoop from that path; otherwise, it initializes a new instance using predefined settings encapsulated in `FACTOR_PROP_SETTING`.

The core functionality of this function revolves around the `FactorRDLoop` class, which extends RDLoop and specializes in handling the research and development loop for financial technology factors. This includes tasks such as factor extraction and evaluation. The main function creates an instance of `FactorRDLoop`, either by loading from a specified path or initializing with default settings.

Once the FactorRDLoop instance is created, the function calls its `run` method, optionally passing the number of steps (`step_n`) to control how many iterations of the loop should be executed. If `step_n` is not provided, the loop runs until it completes all planned iterations or encounters an interruption.

Note: This function can be invoked from a command line interface using the dotenv tool, specifying the path and step number as arguments. It is designed to facilitate continuous development and testing of financial technology factors in an automated manner, allowing researchers and developers to iterate efficiently on their models.
