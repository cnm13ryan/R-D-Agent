## FunctionDef import_module_from_path(module_name, module_path)
**import_module_from_path**: This function dynamically imports a Python module from a specified file path. It utilizes the `importlib` library to create a module specification, instantiate the module, and then execute it within the current environment.

parameters:
· module_name: A string representing the name of the module you wish to import.
· module_path: A string representing the absolute or relative file system path to the Python file containing the module.

Code Description: The function begins by creating a module specification using `importlib.util.spec_from_file_location`. This specification includes all necessary information about the module, such as its name and location. Next, it creates an empty module object based on this specification with `importlib.util.module_from_spec`. Afterward, the module is executed in its own namespace using `spec.loader.exec_module`, which populates the module object with the contents of the file at the specified path. Finally, the function returns the fully initialized module object.

Note: This function is particularly useful when you need to import modules dynamically based on runtime conditions or configurations, such as loading plugins or custom scripts from user-defined locations.

Output Example: If `module_path` points to a Python file containing a class named `MyClass`, calling `import_module_from_path('my_custom_module', '/path/to/my_custom_module.py')` would return the module object. You could then access `MyClass` using `returned_module.MyClass`.
