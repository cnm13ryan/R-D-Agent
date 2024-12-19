## FunctionDef import_module_from_path(module_name, module_path)
**import_module_from_path**: This function dynamically imports a Python module from a specified file path. It allows for the loading of modules at runtime, which can be particularly useful in scenarios where the module's location is determined programmatically or when working with plugins.

parameters:
· module_name: A string representing the name you want to assign to the imported module. This name will be used to reference the module within your code.
· module_path: A string specifying the file path to the Python file that contains the module you wish to import.

Code Description: The function utilizes the `importlib.util` module, which provides utilities for importing modules in a more flexible way than the standard `import` statement. Here's how it works:
1. `spec = importlib.util.spec_from_file_location(module_name, module_path)`: This line creates a module specification from the given file path and assigns it to the variable `spec`. The specification includes information about where the module is located and what its name should be.
2. `module = importlib.util.module_from_spec(spec)`: Using the created specification, this line generates a new module object that corresponds to the specified file location.
3. `spec.loader.exec_module(module)`: This step executes the module in its own namespace, effectively loading it into memory and making its contents available for use.
4. `return module`: Finally, the function returns the newly loaded module object, allowing you to interact with it as if it had been imported using a standard import statement.

Note: Usage points include scenarios where modules are not known until runtime or when developing systems that need to load plugins dynamically. This function is particularly useful in environments like Kaggle competitions where custom scripts might be required for specific tasks and their locations may vary.

Output Example: Mock up a possible appearance of the code's return value.
Assuming there is a Python file located at '/path/to/custom_module.py' with the following content:
```python
def greet():
    print("Hello from the dynamically imported module!")
```
Using `import_module_from_path('custom_module', '/path/to/custom_module.py')` would result in an object that can be used as follows:
```python
loaded_module = import_module_from_path('custom_module', '/path/to/custom_module.py')
loaded_module.greet()  # Outputs: Hello from the dynamically imported module!
```
This demonstrates how a function defined within 'custom_module.py' can be accessed and executed after being dynamically imported.
