## ClassDef RDAT
**RDAT**: RD-Agent's Template class provides a straightforward method to create and render templates using YAML files as data sources.

attributes:
· uri: A string representing the path to the YAML file and the keys within it, formatted as "path.to.file:key1.key2".

Code Description: The RDAT class is designed to simplify the process of loading template data from YAML files and rendering them with specific context. Upon initialization, the class takes a URI that specifies both the location of the YAML file and the nested keys to access the desired template string within that file.

The constructor first inspects the calling stack to determine the directory of the module that instantiated RDAT. This allows for relative paths in the URI to be resolved correctly based on where the RDAT object was created.

Next, it parses the URI into two parts: the path to the YAML file and a series of keys indicating how to navigate through the loaded YAML data structure to find the template string. If the path starts with a dot (.), it is treated as relative to the caller's directory; otherwise, it is considered absolute within a predefined project path.

The specified YAML file is then opened and its contents are loaded into a Python dictionary using PyYAML's safe_load function. The class traverses this dictionary according to the keys provided in the URI to extract the template string, which is stored in the `self.template` attribute.

The `r` method of the RDAT class takes keyword arguments representing the context for rendering the template. It uses Jinja2's Environment and from_string methods to render the template with the given context, returning the rendered string.

Note: The URI format must be correctly specified to ensure that the correct YAML file is loaded and the appropriate keys are used to access the desired template string within it. Additionally, any placeholders in the template string should match the keyword arguments provided to the `r` method for proper rendering.

Output Example: Assuming a YAML file located at "templates.example.yaml" with the following content:

```
greeting:
  formal: "Dear {{ name }},"
  informal: "Hi {{ name }}!"
```

And an RDAT object created with the URI "templates.example:greeting.formal", calling `r(name="Alice")` on this object would return the string "Dear Alice,".
### FunctionDef __init__(self, uri)
**__init__**: Initializes an instance of the RDAT class by parsing a URI to locate and load a specific section from a YAML file, which is then stored in `self.template`.

parameters:
· uri: A string representing the location and path within a YAML file to be loaded. The format can be either "a.b.c:x.y.z" or ".c:x.y.z".

Code Description: Detailed analysis and description.
The function begins by inspecting the calling stack to determine the directory of the module that called this constructor (`__init__`). This is achieved using Python's `inspect` module, which allows for introspection into live objects. The caller's directory is crucial when dealing with relative paths specified in the URI.

Next, the URI provided as an argument is split into two parts: a path part and a YAML path. This splitting occurs at the colon (":"). The path part indicates where to find the YAML file, while the YAML path specifies which nested dictionary keys to traverse within that YAML file to retrieve the desired template.

The function then checks if the path part starts with a dot ("."). If it does, this signifies a relative path from the caller's directory. In such cases, the dot is removed, and the remaining string is transformed into a directory structure by replacing dots with slashes. This new path is appended to the caller's directory to form the full file path of the YAML file.

If the path part does not start with a dot, it is assumed to be an absolute path within a predefined project path (`PROJ_PATH`). Similar to the relative case, dots are replaced with slashes to create the correct directory structure. The `.yaml` suffix is then added to complete the file path.

The YAML file located at this path is opened and read using `yaml.safe_load`, which parses the YAML content into a Python dictionary. This dictionary represents the entire configuration stored in the YAML file.

Following the loading of the YAML content, the function traverses this dictionary according to the keys specified in the YAML path part of the URI. Each key in the YAML path is used sequentially to access nested dictionaries within the loaded YAML content until the final desired template is reached.

Finally, this retrieved template is stored in an instance variable `self.template`, making it accessible for use elsewhere in the RDAT class or by any other code that interacts with an instance of this class.

Note: Usage points.
When using this function, ensure that the URI provided correctly reflects the structure of your YAML files and the nested dictionaries within them. The path part should accurately point to the location of the YAML file relative to either the caller's directory (if starting with a dot) or the project root (`PROJ_PATH`). Incorrect URIs can lead to `FileNotFoundError` if the specified file does not exist, or `KeyError` if the specified keys do not exist within the loaded YAML content.
***
### FunctionDef r(self)
**r**: Render the template with the given context.
parameters:
· **context**: A dictionary of key-value pairs representing variables to be used within the template.

Code Description: The function `r` is designed to render a template string using Jinja2, a popular templating engine for Python. It takes an arbitrary number of keyword arguments (`**context`) which are passed as context variables to the template. Inside the function, an instance of `Environment` from Jinja2 is created with `StrictUndefined`, meaning that any undefined variable in the template will raise an error rather than silently failing or being ignored. The `from_string` method of this environment object is then used to create a `Template` object from the string stored in `self.template`. Finally, the `render` method of the `Template` object is called with the provided context, which replaces placeholders in the template with actual values and returns the rendered string.

Note: This function assumes that `self.template` is a valid Jinja2 template string. It's important to ensure that all variables used within the template are defined in the context passed to this function to avoid runtime errors due to undefined variables.

Output Example: If `self.template` contains the string `"Hello, {{ name }}!"` and the function is called with `r(name="Alice")`, the output will be `"Hello, Alice!"`.
***
