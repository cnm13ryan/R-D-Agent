## FunctionDef compute_metrics_for_classification(y_true, y_pred)
**compute_metrics_for_classification**: This function computes the log loss metric for a classification problem given true labels and predicted probabilities.

parameters:
· y_true: An array-like structure containing the true class labels.
· y_pred: A 2D array-like structure where each row corresponds to an instance and contains the predicted probabilities for each class.

Code Description: The function starts by identifying all unique classes present in the true labels using numpy's `unique` method. It then calculates the log loss using sklearn's `log_loss` function, passing the true labels, predicted probabilities, and the identified classes as parameters. Log loss quantifies the accuracy of a classifier where the prediction input is a probability value between 0 and 1. Lower log loss indicates better performance.

Note: The function assumes that y_pred contains probabilities for all possible classes in y_true. It is crucial to ensure that the predicted probabilities are well-calibrated, meaning they reflect true likelihoods of class membership.

Output Example: If `y_true` is [0, 1] and `y_pred` is [[0.7, 0.3], [0.2, 0.8]], the function might return a log loss value such as 0.4581453659372775. This output represents the average log loss across all instances in the dataset.
## FunctionDef import_module_from_path(module_name, module_path)
**import_module_from_path**: This function dynamically imports a Python module from a specified file path. It allows users to load modules at runtime, which can be particularly useful for loading custom scripts or plugins.

parameters:
· module_name: A string representing the name of the module you wish to import. This name is used internally and does not need to match the filename.
· module_path: A string specifying the file path to the Python file containing the module you want to import.

Code Description: The function utilizes the `importlib.util` module, which provides utilities for importing modules programmatically. It starts by creating a module specification using `spec_from_file_location`, where it takes the desired module name and its file location as arguments. This specification contains all necessary information about how to load the module. Next, it creates a new module based on this specification with `module_from_spec`. The function then executes the module in its own namespace using `exec_module` from the loader attached to the specification. Finally, it returns the newly created and executed module object.

Note: This function is useful when you need to load modules dynamically at runtime, such as when working with plugins or custom scripts that are not known until execution time. It requires that the file path provided points to a valid Python script.

Output Example: If there were a Python file located at '/path/to/my_script.py' containing a class `MyClass`, calling `import_module_from_path('my_custom_module', '/path/to/my_script.py')` would return a module object from which you could access `MyClass` using `module.MyClass`.
