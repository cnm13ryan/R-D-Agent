## FunctionDef import_module_from_path(module_name, module_path)
**import_module_from_path**: This function dynamically imports a Python module from a specified file path. It allows users to load modules at runtime, which can be particularly useful for modularizing code or loading plugins.

parameters:
· module_name: A string representing the name of the module that will be imported. This is used as an identifier within the program.
· module_path: A string representing the absolute or relative file path to the Python file containing the module to be imported.

Code Description: The function utilizes the `importlib.util` module, which provides utilities for importing modules programmatically. It starts by creating a module specification using `spec_from_file_location`, where it takes the desired module name and its file location as arguments. This specification includes all necessary information about how to load the module. Next, it creates a new module based on this specification with `module_from_spec`. Finally, it executes the module in its own namespace using `exec_module` from the loader attached to the specification, effectively running the code within the newly created module object. The function then returns this module object, which can be used like any other imported module.

Note: This function is particularly useful when you need to load modules dynamically based on user input or configuration files, or when working with plugins that are not known at compile time. It requires the file path to point to a valid Python file and the module name should follow Python's naming conventions for identifiers.

Output Example: If there is a Python file located at '/path/to/my_module.py' containing a function `greet` defined as follows:

```python
def greet():
    print("Hello, world!")
```

Using `import_module_from_path('my_module', '/path/to/my_module.py')` would return a module object. You could then call the `greet` function from this module like so:

```python
loaded_module = import_module_from_path('my_module', '/path/to/my_module.py')
loaded_module.greet()  # This will print "Hello, world!"
```
## FunctionDef MCRMSE(y_true, y_pred)
**MCRMSE**: This function calculates the Mean Column-wise Root Mean Square Error (MCRMSE) between true values and predicted values. It is particularly useful in scenarios where predictions are made for multiple related targets, such as different aspects of a single phenomenon.

parameters:
· y_true: A numpy array representing the actual target values.
· y_pred: A numpy array representing the predicted target values.

Code Description: The function MCRMSE computes the Root Mean Square Error (RMSE) for each column separately when comparing the true and predicted values. It then calculates the mean of these RMSEs across all columns to produce a single metric that represents the average error magnitude across multiple targets or features. This approach is beneficial in multi-target regression problems where different targets may have varying scales or importance.

Note: The function assumes that y_true and y_pred are numpy arrays with the same shape, where each column corresponds to a separate target variable. It also assumes that the data has been preprocessed appropriately (e.g., normalized) if necessary for accurate error estimation.

Output Example: If y_true is [[1, 2], [3, 4]] and y_pred is [[1.1, 1.9], [2.8, 4.1]], the function would first calculate the RMSE for each column separately:
- For the first column: RMSE = sqrt(((1 - 1.1)^2 + (3 - 2.8)^2) / 2) ≈ 0.1
- For the second column: RMSE = sqrt(((2 - 1.9)^2 + (4 - 4.1)^2) / 2) ≈ 0.1

The MCRMSE would then be the mean of these values, which is approximately 0.1 in this case.
