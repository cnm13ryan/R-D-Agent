## FunctionDef import_module_from_path(module_name, module_path)
**import_module_from_path**: This function dynamically imports a Python module from a specified file path. It utilizes the `importlib` library to create a module specification, instantiate the module, and execute it within the current environment.

parameters:
· module_name: A string representing the name of the module to be imported.
· module_path: A string representing the filesystem path to the Python file containing the module.

Code Description: The function begins by creating a module specification using `importlib.util.spec_from_file_location`, which takes two arguments: the desired name for the module and the path to the file that contains the module's code. This specification object holds all necessary information about how the module should be loaded.

Next, `importlib.util.module_from_spec` is called with the specification object as its argument. This function creates a new module based on the provided specification. The newly created module is not yet populated with any of its functions or classes; it's just an empty shell at this point.

The module is then executed in the current environment using `spec.loader.exec_module(module)`. This step populates the module object with all the defined functions, classes, and variables from the file specified by `module_path`.

Finally, the function returns the fully loaded module object. This allows the caller to use the imported module as if it had been statically imported at the top of their script.

Note: Usage points include scenarios where you need to load modules dynamically based on user input or configuration files, or when working with plugins that are not known until runtime.

Output Example: Mock up a possible appearance of the code's return value.
Assuming there is a Python file located at '/path/to/my_module.py' containing:
```python
def greet():
    print("Hello from my module!")
```
Calling `import_module_from_path('my_dynamic_module', '/path/to/my_module.py')` would return a module object that can be used as follows:
```python
dynamic_module = import_module_from_path('my_dynamic_module', '/path/to/my_module.py')
dynamic_module.greet()  # Outputs: Hello from my module!
```
