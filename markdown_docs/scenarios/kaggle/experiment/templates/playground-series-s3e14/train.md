## FunctionDef import_module_from_path(module_name, module_path)
**import_module_from_path**: This function dynamically imports a Python module from a specified file path. It utilizes the `importlib` library to create a module specification, instantiate the module, and execute it within the current environment.

parameters:
· module_name: A string representing the name of the module you wish to import.
· module_path: A string representing the absolute or relative file system path to the Python file containing the module.

Code Description: The function begins by creating a module specification using `importlib.util.spec_from_file_location`. This specification includes metadata about the module, such as its location and name. Next, it creates an empty module object based on this specification with `importlib.util.module_from_spec`. Afterward, the module is executed in the current environment using `spec.loader.exec_module(module)`, which populates the module object with all the functions, classes, and variables defined in the file at `module_path`. Finally, the function returns the fully loaded module object.

Note: This approach is particularly useful when you need to import modules dynamically at runtime based on user input or configuration files. It allows for flexible code loading without hardcoding imports at the top of your scripts.

Output Example: If there were a Python file located at '/path/to/my_module.py' containing a function `greet(name)`, calling `import_module_from_path('my_module', '/path/to/my_module.py')` would return a module object. You could then access and use the `greet` function from this returned module as follows:

```python
loaded_module = import_module_from_path('my_module', '/path/to/my_module.py')
loaded_module.greet('Alice')  # Assuming greet prints or returns a greeting message for 'Alice'
```
