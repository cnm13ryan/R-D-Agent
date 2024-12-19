## ClassDef LoopMeta
**LoopMeta**: A metaclass designed to manage and combine a list of steps from base classes into a new class. This is particularly useful for creating workflows where each step can be defined as a method within a class hierarchy.

attributes:
· clsname (str): The name of the new class being created.
· bases (tuple): A tuple containing the base classes that the new class inherits from.
· attrs (dict): A dictionary holding the attributes and methods of the new class, including any steps defined as callable objects.

Code Description: LoopMeta is a metaclass that overrides the __new__ method to customize class creation. The primary function of this metaclass is to gather all callable methods (steps) from the base classes and combine them into a single list stored in the 'steps' attribute of the new class. This process ensures that steps are inherited and can be overridden or extended by subclasses.

The _get_steps static method is used recursively to traverse the inheritance hierarchy, collecting all unique steps from each level of the hierarchy. It checks for callable attributes in each base class and adds them to a list if they are not already present, ensuring no duplicates.

In the __new__ method, after gathering all inherited steps, it iterates over the attributes of the new class. If an attribute is a callable (method) that does not start with double underscores (indicating special methods), it is added to the 'steps' list unless it is already present or overrides an existing step from a base class.

Note: Usage points include defining workflow steps in a structured and inheritable manner, allowing for complex workflows to be built up from simpler components. This metaclass can be particularly useful in scenarios where a series of operations need to be performed in a specific order, such as data processing pipelines or automated testing frameworks.

Output Example: Mock up a possible appearance of the code's return value.
Assuming we have two classes defined using LoopMeta:

```python
class WorkflowStep1(metaclass=LoopMeta):
    def step_one(self):
        print("Executing Step One")

class WorkflowStep2(WorkflowStep1, metaclass=LoopMeta):
    def step_two(self):
        print("Executing Step Two")
```

The 'steps' attribute of the WorkflowStep2 class would be a list containing references to both `step_one` and `step_two`, reflecting the combined steps from its base classes and itself. This allows for easy iteration over all defined steps in the workflow, ensuring that each step is executed in the correct order as defined by the inheritance hierarchy.
### FunctionDef _get_steps(bases)
**_get_steps**: This function recursively gathers all the `steps` from a given tuple of base classes and combines them into a single list, ensuring no duplicates.

parameters:
· bases: A tuple containing the base classes from which to retrieve steps.

Code Description: The function `_get_steps` is designed to traverse through each class in the provided `bases` tuple. For each base class, it first recursively calls itself with the base class's own base classes (`base.__bases__`) to ensure that all inherited steps are collected. It then retrieves any `steps` attribute directly defined on the current base class using `getattr(base, "steps", [])`. The combination of these two sources forms a list of steps for each base class. To avoid adding duplicate steps, it checks if a step is already in the `steps` list before appending it.

Note: This function is typically used within a metaclass to aggregate methods or functions that are intended to be executed as part of a workflow or process defined across multiple classes and their hierarchies.

Output Example: If there are two base classes, `Base1` and `Base2`, where `Base1.steps = [step1, step2]` and `Base2.steps = [step2, step3]`, calling `_get_steps((Base1, Base2))` would return `[step1, step2, step3]`. The function ensures that each step is included only once in the final list.
***
### FunctionDef __new__(cls, clsname, bases, attrs)
**__new__**: This function creates a new class by combining steps from both base classes and the current class attributes. It ensures that each step is unique, avoiding duplicates across the inheritance hierarchy.

**parameters**:
· clsname (str): The name of the new class being created.
· bases (tuple): A tuple containing the base classes from which to inherit.
· attrs (dict): A dictionary of attributes for the new class, including methods and properties.

**Code Description**: The function starts by calling `_get_steps` with `bases` as an argument to gather all steps defined in the base classes. It then iterates over each attribute in `attrs`. If the attribute is a callable (a method or function) and does not start with double underscores, it checks if this step is already included in the `steps` list. If not, it appends the step to ensure that only unique steps are added. This process ensures that any overridden methods in subclasses do not result in duplicate entries. Finally, the modified `attrs` dictionary, which now includes a `steps` attribute containing all unique steps, is passed to the superclass's `__new__` method to create and return the new class.

**Note**: This function is part of a metaclass (`LoopMeta`) designed to manage workflow or process steps across multiple classes. It ensures that each step in the workflow is uniquely identified and executed only once, even if it appears in multiple base classes or is overridden in subclasses.

**Output Example**: If `Base1` has `steps = [step1, step2]`, `Base2` has `steps = [step2, step3]`, and a subclass defines an additional method `step4`, calling `__new__(Subclass, (Base1, Base2), {...})` would result in a new class with `steps = [step1, step2, step3, step4]`. Each step is included only once, and the order reflects their first appearance in the inheritance hierarchy or definition.
***
## ClassDef LoopTrace
**LoopTrace**: The LoopTrace class is designed to capture the timing information of a loop execution within a workflow process. It records both the start and end times of each loop iteration, which can be useful for performance analysis and debugging.

**attributes**:
· start: Represents the datetime when the loop iteration begins.
· end: Represents the datetime when the loop iteration ends.

**Code Description**: The LoopTrace class is a simple data structure with two attributes, `start` and `end`, both of type `datetime.datetime`. These attributes are used to store the timestamps marking the beginning and the end of a specific loop iteration. This information can be crucial for understanding how long each iteration takes and identifying any performance bottlenecks in the workflow.

The class is utilized within the LoopBase class, particularly in the run method where it captures the start and end times of each step execution during the workflow process. After executing a step, an instance of LoopTrace is created with the current timestamps and appended to the loop_trace dictionary associated with the specific loop index. This allows for detailed tracking of how long each step takes across multiple iterations.

**Note**: Usage points include performance monitoring, debugging, and optimization of workflows by analyzing the duration of individual steps or entire loops. Developers can use this data to identify slow-running parts of their workflow and make informed decisions on how to improve efficiency. Beginners should understand that LoopTrace is a tool for capturing timing information and can be extended or modified as needed for more detailed analysis.
## ClassDef LoopBase
Doc is waiting to be generated...
### FunctionDef __init__(self)
**__init__**: Initializes a new instance of the LoopBase class, setting up essential attributes to manage loop iterations and step executions within a workflow.

parameters:
· None: The __init__ method does not accept any parameters.

Code Description: Upon instantiation, several key attributes are initialized to facilitate the management and tracking of loop iterations and their associated steps. 
- `loop_idx` is set to 0, representing the current index of the loop iteration.
- `step_idx` is also initialized to 0, indicating that the next step to be executed in the workflow is at index 0.
- `loop_prev_out` is an empty dictionary designed to store the results of steps from the current loop iteration. This allows for referencing previous outputs when necessary during subsequent steps or iterations.
- `loop_trace` is a defaultdict with lists of LoopTrace objects as values, where each key corresponds to a specific loop index. This structure enables detailed tracking and analysis of timing information for each step within every loop iteration by storing instances of LoopTrace that capture start and end times.
- `session_folder` is set to the path obtained from combining `logger.log_trace_path` with "__session__". This attribute specifies where session-related logs or traces will be stored, facilitating later retrieval and analysis.

Note: Developers can utilize these attributes to manage complex workflows involving multiple loop iterations and steps. The initialization sets up a clean state for each new instance of LoopBase, ensuring that previous data does not interfere with subsequent executions. Beginners should understand the role of each attribute in maintaining workflow integrity and enabling detailed performance tracking through the use of LoopTrace objects.
***
### FunctionDef run(self, step_n)
**run**: This function executes a series of steps defined within an instance of the LoopBase class. It can run indefinitely until an error occurs, a KeyboardInterrupt is raised, or a specified number of steps have been completed.

**parameters**:
· step_n: An integer indicating how many steps to run; if set to None, the function will run indefinitely until interrupted by an error or manually stopped with a KeyboardInterrupt.

**Code Description**: The `run` method begins by setting up a progress bar using the `tqdm` library, which provides a visual representation of the workflow's progress. This progress bar is configured to display the total number of steps and updates after each step execution.

The function enters an infinite loop where it checks if `step_n` has been provided and decrements it by one with each iteration until it reaches zero, at which point the loop breaks. If `step_n` is None, the loop continues indefinitely.

Within the loop, the current loop index (`li`) and step index (`si`) are retrieved. The start time of the step execution is recorded using `datetime.datetime.now(datetime.timezone.utc)`. The function then retrieves the name of the next step from the `steps` list and uses `getattr` to dynamically call the corresponding method on the instance.

The method associated with the current step is executed, passing in the previous outputs stored in `loop_prev_out`. If the execution is successful, the output is stored back into `loop_prev_out`, and a new `LoopTrace` object is created to capture the start and end times of the step. This trace is then appended to the `loop_trace` dictionary under the current loop index.

If an exception of type `skip_loop_error` occurs during step execution, a warning is logged, and the function moves on to the next loop by resetting the step index to zero and incrementing the loop index. If a `CoderError` is raised, it logs a warning and also resets the step index to zero but does not increment the loop index, effectively retrying the current loop.

After each successful step execution, the progress bar is updated with the current loop and step indices and the name of the executed step. The step index is then incremented, and if it reaches the total number of steps, both the step index and the previous outputs are reset, indicating the start of a new loop iteration. Additionally, the progress bar is reset to reflect the beginning of the new loop.

Finally, after each step execution, the current state of the object is serialized and saved to a file using the `dump` method. The file path includes the loop index and step index, allowing for detailed tracking and recovery of the workflow's state at any point during its execution.

**Note**: This function is essential for executing workflows defined in an instance of LoopBase, providing mechanisms for error handling, progress tracking, and state persistence. It is particularly useful for long-running processes where monitoring and checkpointing are necessary. Developers can use this method to automate the execution of complex workflows with built-in support for recovery from errors and interruptions. Beginners should understand that `run` orchestrates the workflow's steps, handles exceptions gracefully, and maintains a record of each step's execution time and state.
***
### FunctionDef dump(self, path)
**dump**: This function serializes the current instance of the class to a file using Python's `pickle` module. It is designed to save the state of an object, allowing it to be restored later.

**parameters**:
· path: A string or a Path object representing the file path where the serialized data will be stored.

**Code Description**: The function begins by ensuring that the provided path is converted into a `Path` object if it isn't already. It then creates any necessary directories in the path to ensure that the file can be written without errors, using `mkdir(parents=True, exist_ok=True)`. This step is crucial for preventing runtime errors related to missing directories.

The function opens the specified file in binary write mode (`wb`). Using the `pickle.dump` method, it serializes the current instance of the class (referred to as `self`) and writes this serialized data directly into the file. The use of binary mode ensures that the data is stored efficiently and can be accurately restored later.

**Note**: Usage points include saving the state of an object for debugging purposes, creating checkpoints during long-running processes, or persisting objects across different program executions. It's important to note that while `pickle` is convenient, it should not be used to deserialize data from untrusted sources due to security risks.
***
### FunctionDef load(cls, path)
**load**: This function loads a session from a specified file path using pickle serialization. It sets up logging paths based on the loaded session's folder structure, truncates logger storage to the end time of the last loop trace entry, and returns the loaded session object.

parameters:
· path: A string or Path object representing the file path where the session data is stored.

Code Description: The function begins by ensuring that the provided path is a Path object. It then opens this file in binary read mode ("rb"). Using pickle.load(), it deserializes the content of the file into a Python object, which should be an instance of a session class or similar structure. After loading the session, it configures the logger's trace path to point to the parent directory of the session folder. This setup is likely used for tracking and debugging purposes within the application.

Next, the function identifies the maximum loop index from the keys in the session's loop_trace dictionary. It uses this index to access the last entry in the corresponding list of loop traces. The end time of this final trace entry is then used to truncate the logger's storage, effectively removing any log entries that occurred after the session ended.

Finally, the function returns the loaded session object, which can be used for further processing or analysis within the application.

Note: This function assumes that the file at the specified path contains a valid serialized session object. It also relies on the presence of certain attributes and methods in the session object (e.g., `session_folder`, `loop_trace`) and the logger module (e.g., `set_trace_path`, `storage.truncate`). Developers should ensure these components are correctly implemented to avoid runtime errors.

Output Example: Assuming a valid session file is located at '/path/to/session.pkl', calling `load('/path/to/session.pkl')` would return a session object with all its attributes and methods intact, ready for use. The logger's trace path would be set to '/path/to/', and the storage would be truncated to reflect the end time of the last loop in the session.
***
