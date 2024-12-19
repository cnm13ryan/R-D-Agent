## FunctionDef get_module_by_module_path(module_path)
**get_module_by_module_path**: This function loads a Python module from a specified path. It accepts either a string representing the file path to a .py file or a dot-separated string representing the module's import path.

parameters:
· module_path: A string that represents the path to the module, which can be in the form of a file path (e.g., 'a/b/c/d.py') or an import path (e.g., 'a.b.c.d'). It can also accept a ModuleType object directly. If None is provided, it raises a ModuleNotFoundError.

Code Description: The function begins by checking if the `module_path` parameter is None and raises a ModuleNotFoundError if true. Next, it checks whether the `module_path` is already an instance of ModuleType. If so, it assumes that the module has already been loaded and returns it directly. 

If `module_path` is not a ModuleType object, the function proceeds to determine how to load the module based on its format. If the path ends with '.py', indicating a file path, the function constructs a valid Python module name by removing any leading non-alphanumeric characters (except underscores) and replacing all other non-alphanumeric characters (except underscores) with nothing, and slashes with underscores. It then uses `importlib.util.spec_from_file_location` to create a module specification from the given location and file name. A new module is created using `module_from_spec`, added to `sys.modules`, and executed with `exec_module`.

If the path does not end with '.py', it is treated as an import path, and the function uses `importlib.import_module` to load the module.

Note: This function is useful for dynamically importing modules at runtime based on a string representation of their location. It supports both file paths and standard Python import paths, making it versatile for various use cases where module loading needs to be flexible.

Output Example: If 'utils/math_operations.py' is passed as `module_path`, the function will return the loaded module object corresponding to that file, allowing access to its functions and classes through this object. For example, if 'math_operations.py' contains a function named `add`, you can call it using `loaded_module.add(2, 3)`.
## FunctionDef convert2bool(value)
**convert2bool**: This function aims to convert a given input value into a boolean type. It is particularly useful when dealing with unstable return values from Language Learning Models (LLMs), which may not consistently return boolean values in a standard format.

parameters:
· value: The input value that needs to be converted to a boolean. This parameter can accept either a string or a boolean value.

Code Description: The function first checks if the input value is of type string. If it is, the function converts the string to lowercase and removes any leading or trailing whitespace characters. It then checks if this cleaned-up string matches any of the predefined strings that represent a true value ("true", "yes", "ok"). If there's a match, the function returns True. Similarly, it checks for false values ("false", "no") and returns False in such cases. If the input string does not match any known boolean representation, the function raises a ValueError indicating that the conversion is not possible.

If the input value is already of type boolean, the function simply returns this value without any modification. For all other types of input values, the function raises a ValueError to indicate that it cannot convert the given value to a boolean.

Note: This function is designed to handle common string representations of boolean values and expects inputs in lowercase or mixed case strings that can be easily converted to lowercase for comparison. It does not support numeric values (like 0 or 1) as valid inputs for conversion.

Output Example: 
- convert2bool("True") returns True
- convert2bool("no") returns False
- convert2bool(True) returns True
- convert2bool("maybe") raises ValueError: Can not convert maybe to bool
