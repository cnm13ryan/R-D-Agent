## FunctionDef import_module_from_path(module_name, module_path)
**import_module_from_path**: This function dynamically imports a Python module from a specified file path, allowing for flexible module loading at runtime.

parameters:
· module_name: A string representing the name of the module to be imported.
· module_path: A string representing the file system path to the Python file containing the module.

Code Description: The function utilizes the `importlib.util` module, which provides utilities for importing modules programmatically. It first creates a module specification using `spec_from_file_location`, specifying both the name and the location of the module. Then, it constructs a new module based on this specification with `module_from_spec`. Finally, it executes the module in its own namespace to initialize it, making all defined classes, functions, and variables available for use. The function returns the imported module object.

Note: This approach is particularly useful when modules are not located within the standard Python path or when you need to load a module dynamically based on user input or configuration settings.

Output Example: If there were a file at '/path/to/my_module.py' containing a class `MyClass`, calling `import_module_from_path('my_module', '/path/to/my_module.py')` would return a module object from which `MyClass` could be accessed as `module.MyClass`.
