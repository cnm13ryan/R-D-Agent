## ClassDef RDAgentException
**RDAgentException**: Custom exception class derived from Python's built-in Exception class, designed to handle specific errors within the RDAgent framework.

attributes:
· No explicit attributes: This custom exception does not define any additional attributes beyond those inherited from the base Exception class.

Code Description: Detailed analysis and description.
The RDAgentException class is a simple extension of Python’s standard Exception class. It serves as a specialized type of exception that can be raised when specific error conditions occur within the RDAgent framework, particularly in scenarios where custom handling or logging might be required. By defining this custom exception, developers can catch and handle errors more precisely than using generic exceptions.

In the provided context, the RDAgentException is used within the __new__ method of a SingletonBaseClass to enforce the use of keyword arguments (kwargs) instead of positional arguments (args). This enforcement is implemented by checking if any positional arguments are passed. If they are, an instance of RDAgentException is raised with a specific message: "Please only use kwargs in Singleton to avoid misunderstanding."

This approach ensures that all instances created through the SingletonBaseClass constructor are uniquely identified based on their keyword arguments, which simplifies the management and retrieval of singleton instances. The exception serves as a clear signal to developers about the expected usage pattern, enhancing code readability and maintainability.

Note: Usage points.
Developers should use RDAgentException when they need to raise an error specific to the RDAgent framework, particularly in cases where enforcing certain conditions or patterns is necessary. In the example provided, it is used to enforce the use of keyword arguments in a Singleton pattern implementation, ensuring that each instance is uniquely identifiable by its parameters. This practice helps prevent common errors and misunderstandings related to object instantiation and management within the RDAgent framework.
## ClassDef SingletonBaseClass
**SingletonBaseClass**: This class serves as a base class to implement the Singleton design pattern without requiring the use of metaclasses. It ensures that only one instance of a derived class is created, even when multiple instantiation attempts are made with the same arguments.

attributes:
· _instance_dict: A dictionary that stores instances of classes derived from SingletonBaseClass. The keys in this dictionary are hashes of the class name and its initialization parameters, ensuring that each unique set of parameters results in a single instance.

Code Description: 
The SingletonBaseClass overrides the __new__ method to control the creation of new instances. It checks if any positional arguments (args) are provided during instantiation and raises an RDAgentException if they are, enforcing the use of keyword arguments (kwargs) for clarity and consistency. The class name along with its initialization parameters is hashed to create a unique key in _instance_dict. If no instance exists for this hash, it creates a new one using super().__new__(cls). Otherwise, it returns the existing instance.

The __reduce__ method is overridden to prevent pickling of instances of SingletonBaseClass. This is necessary because when an object is unpickled, its __new__ method does not receive the original kwargs used during initialization, making it impossible to retrieve the correct singleton instance from _instance_dict.

Note: Usage points.
When defining a class that should follow the Singleton pattern, simply inherit from SingletonBaseClass instead of using a metaclass. Ensure all parameters are passed as keyword arguments to avoid exceptions and ensure proper singleton behavior. Be aware that instances of this class cannot be pickled due to the overridden __reduce__ method.

Output Example: Mock up a possible appearance of the code's return value.
```python
class MySingleton(SingletonBaseClass):
    def __init__(self, param1=None, param2=None):
        self.param1 = param1
        self.param2 = param2

# Creating an instance with specific parameters
instance1 = MySingleton(param1="value1", param2="value2")

# Attempting to create another instance with the same parameters
instance2 = MySingleton(param1="value1", param2="value2")

# Both variables point to the same object
assert id(instance1) == id(instance2)

# Trying to create an instance using positional arguments will raise an exception
try:
    invalid_instance = MySingleton("value1", "value2")
except RDAgentException as e:
    print(e)  # Output: Please only use kwargs in Singleton to avoid misunderstanding.
```
### FunctionDef __new__(cls)
**__new__**: This method is responsible for creating a new instance of the SingletonBaseClass. It ensures that only one instance of the class exists per unique set of keyword arguments, adhering to the singleton pattern.

parameters:
· cls: The class itself (SingletonBaseClass).
· *args: Variable length argument list. In this implementation, positional arguments are not allowed and will raise an exception.
· **kwargs: Arbitrary keyword arguments that uniquely identify the instance.

Code Description: The __new__ method first checks if any positional arguments have been provided. If so, it raises an RDAgentException with a message indicating that only keyword arguments should be used to avoid misunderstandings. This is done to ensure that each instance of the class can be uniquely identified by its parameters, which is crucial for maintaining the singleton pattern.

The method then constructs a list containing the module and class name, followed by any positional arguments (though these are not expected) and sorted keyword arguments. This list is converted into a tuple and hashed to create a unique identifier for the instance based on its parameters.

If this hash does not exist in the _instance_dict dictionary, indicating that no such instance has been created before, a new instance of the class is created using super().__new__(cls) and stored in the dictionary with the hash as the key. If an instance with the same parameters already exists, it simply returns the existing instance from the dictionary.

This approach ensures that for any given set of keyword arguments, only one instance of the SingletonBaseClass will be created, adhering to the singleton pattern while allowing flexibility through different sets of parameters.

Note: Usage points.
Developers should use this method when implementing classes that require a singleton pattern with unique instances based on specific parameters. By using keyword arguments exclusively, developers can ensure that each instance is uniquely identifiable and managed efficiently within the application.

Output Example: If you were to create an instance of SingletonBaseClass with the same set of keyword arguments multiple times, only one instance would be created:

```python
instance1 = SingletonBaseClass(param1="value1", param2="value2")
instance2 = SingletonBaseClass(param1="value1", param2="value2")

# Both variables point to the same instance
assert instance1 is instance2
```

If you were to create an instance using positional arguments, an RDAgentException would be raised:

```python
try:
    instance3 = SingletonBaseClass("value1", "value2")
except RDAgentException as e:
    print(e)  # Output: Please only use kwargs in Singleton to avoid misunderstanding.
```
***
### FunctionDef __reduce__(self)
**__reduce__**: This method is designed to prevent instances of a class from being pickled by raising a `PicklingError`. It is particularly useful in classes where the singleton pattern is implemented, as the standard pickling mechanism does not handle singletons properly.

parameters:
· None: The __reduce__ method does not accept any parameters. It is called automatically during the pickling process.

Code Description: Detailed analysis and description.
The __reduce__ method is a special method in Python that defines how an object should be reduced to a form suitable for serialization (pickling). In this implementation, the method deliberately raises a `PicklingError` with a message indicating that instances of the class cannot be pickled. This approach is taken because when objects are unpickled, they do not receive the keyword arguments (`kwargs`) used during their initial creation. As a result, it becomes challenging to ensure that the correct singleton instance is retrieved, which could lead to incorrect behavior or multiple instances being created instead of maintaining the singleton pattern.

Note: Usage points.
This method should be implemented in any class where the singleton pattern is crucial and pickling might inadvertently create new instances or disrupt the singleton state. By raising a `PicklingError`, developers are explicitly informed that they cannot pickle objects of this class, thus preventing potential issues related to object serialization and deserialization in a singleton context.
***
## FunctionDef parse_json(response)
**parse_json**: This function takes a JSON formatted string as input and attempts to parse it into a Python object. If the parsing fails due to an invalid JSON format, it raises a ValueError with a descriptive error message.

parameters:
· response: A string containing JSON data that needs to be parsed into a Python object.

Code Description: The function starts by attempting to parse the 'response' parameter using the `json.loads()` method from the json module. This method converts a JSON formatted string into its corresponding Python representation (such as dictionaries, lists, etc.). If the parsing is successful, the resulting Python object is returned immediately. However, if the input string is not properly formatted as JSON, a `JSONDecodeError` will be raised by `json.loads()`. The function catches this exception and then constructs an error message that includes the original response for debugging purposes. This error message is then used to raise a `ValueError`, which provides more context about what went wrong during the parsing process.

Note: It is important to ensure that the input string 'response' is correctly formatted as JSON before passing it to this function, otherwise a ValueError will be raised with an explanatory message. Developers should handle this exception in their code to provide a better user experience or for logging purposes.

Output Example: If the input string is '{"name": "John", "age": 30}', the output of parse_json would be {'name': 'John', 'age': 30}, which is a Python dictionary. If the input string is not valid JSON, such as '{"name": "John", "age": 30', (missing closing brace), a ValueError will be raised with the message: "Failed to parse response: {"name": "John", "age": 30, please report it or help us to fix it."
## FunctionDef similarity(text1, text2)
**similarity**: This function calculates a similarity score between two input strings using the Levenshtein distance algorithm under the hood, which is wrapped by the `fuzz.ratio` method from the FuzzyWuzzy library. The result is an integer representing the percentage of similarity between the two strings.

parameters:
· text1: A string for comparison. If the provided value is not a string, it defaults to an empty string.
· text2: Another string for comparison. Similarly, if the provided value is not a string, it defaults to an empty string.

Code Description: The function first checks whether both `text1` and `text2` are strings. If either of them is not a string, it assigns an empty string to that variable. This ensures that the function can handle non-string inputs gracefully without causing errors. After ensuring that both variables are strings, the function uses the `fuzz.ratio` method from the FuzzyWuzzy library to compute the similarity score between `text1` and `text2`. The `fuzz.ratio` method calculates a Levenshtein distance ratio, which is then converted to an integer percentage indicating how similar the two strings are. The result is cast to an integer type explicitly, although this casting might not be strictly necessary as `fuzz.ratio` already returns an integer value.

Note: Usage points include comparing textual data for similarity in applications such as duplicate detection, fuzzy matching, or content recommendation systems. It's important to note that the function treats non-string inputs as empty strings, which may lead to unexpected results if not handled carefully by the caller.

Output Example: If `text1` is "hello world" and `text2` is "hallo wurld", the function might return a similarity score of 90, indicating that the two strings are quite similar.
## FunctionDef import_class(class_path)
**import_class**: This function dynamically imports a class from a given string path. It is particularly useful when the module and class names are determined at runtime.

parameters:
· class_path: A string representing the full path to the class, including its module path. For example, "scripts.factor_implementation.baselines.naive.one_shot.OneshotFactorGen".

Code Description: The function takes a single argument, `class_path`, which is expected to be a string in dot notation indicating the location of the class within the project's modules. It splits this string into two parts using the last period as a delimiter: everything before the last period represents the module path, and everything after it is the class name. The function then uses Python's `importlib.import_module` to import the specified module dynamically. Finally, it retrieves the class from the imported module using the `getattr` function by passing the module object and the class name as arguments.

Note: This function requires that the module path provided in `class_path` is valid and accessible within the current Python environment's path. Additionally, the class name must exist within the specified module to avoid an AttributeError.

Output Example: If the input string is "scripts.factor_implementation.baselines.naive.one_shot.OneshotFactorGen", the function will return the `OneshotFactorGen` class defined in the `one_shot.py` file located under the `scripts.factor_implementation.baselines.naive` directory. The returned value can then be instantiated or used as needed within the calling code.
## ClassDef CacheSeedGen
**CacheSeedGen**: A global seed generator designed to produce a sequence of seeds specifically for caching purposes. This class supports the feature `use_auto_chat_cache_seed_gen` claim, ensuring that each cache operation can be reproducible with the same initial seed.

attributes:
· No explicit attributes are defined in the class documentation; however, it maintains an internal state through the random module's seed setting.

Code Description: The CacheSeedGen class initializes with a predefined seed value from LLM_SETTINGS.init_chat_cache_seed. It provides methods to set a new seed and generate the next seed in the sequence. The `set_seed` method configures the random number generator with a given integer, ensuring that subsequent calls to `get_next_seed` will produce a predictable sequence of numbers based on this initial value. The `get_next_seed` method returns a random integer between 0 and 10000, inclusive.

Note: It is important to understand that the seed generated by CacheSeedGen is distinct from regular seeds used in other contexts. Additionally, if the cache is cleared or reset, setting the same initial seed will not guarantee the exact reproduction of previous QA traces due to potential changes in the underlying data or state.

Output Example: If `set_seed(123)` is called and then `get_next_seed()` is invoked multiple times, it might produce a sequence like 4567, 8901, 2345, etc., each time the program runs with the same initial seed. However, if the cache state changes between runs, even with the same seed, the QA trace may differ.
### FunctionDef __init__(self)
**__init__**: This function initializes an instance of the `CacheSeedGen` class by setting a seed for reproducibility purposes.
parameters:
· None: The constructor does not accept any parameters.

Code Description: Upon instantiation, the `__init__` method calls the `set_seed` method with a predefined value from `LLM_SETTINGS.init_chat_cache_seed`. This action sets the initial seed for Python's random number generator to ensure that any random processes initiated by this class instance will produce consistent and reproducible results. The use of a specific seed is crucial in scenarios where maintaining consistency across different runs or environments is necessary, such as in machine learning applications where model training outcomes need to be replicable.

Note: This initialization step is essential for ensuring that the caching mechanism within `CacheSeedGen` behaves predictably, which can be particularly important in development and testing phases. By setting a fixed seed during initialization, developers can rely on consistent behavior from random operations, facilitating easier debugging and validation of system performance.
***
### FunctionDef set_seed(self, seed)
**set_seed**: This function initializes the random seed for reproducibility of random processes within a program.
parameters:
· seed: An integer value used to set the seed for Python's built-in random number generator.

Code Description: The function `set_seed` takes an integer parameter named `seed`. It uses this integer to initialize the state of Python's built-in random number generator. By setting the seed, it ensures that any subsequent calls to functions from the `random` module will produce a predictable sequence of numbers. This is particularly useful in scenarios where reproducibility of results is crucial, such as in scientific experiments or machine learning model training.

Note: The function is utilized within the `CacheSeedGen` class during its initialization to set an initial seed for caching purposes, as seen in the constructor method of `CacheSeedGen`. Additionally, it is called by `_subprocess_wrapper` to ensure that any subprocesses started with this wrapper also use a fixed starting seed, which helps maintain consistency across different runs and environments.
***
### FunctionDef get_next_seed(self)
**get_next_seed**: This function generates a random integer to serve as a seed value.
parameters:
· None: The function does not accept any parameters.

Code Description: The `get_next_seed` function is designed to produce a random integer between 0 and 10,000 inclusive. It utilizes Python's built-in `random.randint()` method for generating the random number. This method takes two arguments, the start and end of the range (both inclusive), and returns a random integer within that range.

The function is particularly useful in scenarios where reproducibility of results is crucial but some randomness is also required, such as in simulations or machine learning experiments where different seeds can lead to different outcomes. By generating a seed value, it allows for controlled randomness, making the process repeatable when the same seed is used under identical conditions.

Note: The function does not take any input parameters and always returns an integer within the specified range (0 to 10,000). It's important to be aware that the use of `random.randint()` may not be suitable for cryptographic purposes due to its simplicity and predictability. For such applications, consider using a more secure method from Python's `secrets` module.

Output Example: A possible return value from this function could be 4567. Each call to `get_next_seed` will likely produce a different number within the range of 0 to 10,000, unless the random seed is explicitly set before calling the function.
***
## FunctionDef _subprocess_wrapper(f, seed, args)
**_subprocess_wrapper**: This function acts as a wrapper to ensure that any subprocess initiated through it starts with a fixed seed value, aiding in reproducibility of results.
parameters:
· f: A callable object (function) that represents the process or task to be executed within the subprocess.
· seed: An integer used to set the random seed for reproducibility purposes.
· args: A list containing arguments that will be passed to the function `f`.

Code Description: The `_subprocess_wrapper` function is designed to encapsulate another function call (`f`) with a specific behavior. Before executing the provided callable `f`, it sets a fixed seed using the `set_seed` method of an object named `LLM_CACHE_SEED_GEN`. This ensures that any random processes initiated within the function `f` will produce consistent and reproducible results across different runs. The function then proceeds to call `f` with the arguments provided in the `args` list, passing along any return values from `f`.

Note: This wrapper is particularly useful when dealing with functions that involve randomness or stochastic processes, such as those found in machine learning models or simulations. By ensuring a fixed seed, developers can reproduce results accurately and compare outcomes across different environments or runs.

Output Example: If `_subprocess_wrapper` were used to call a function `generate_random_numbers(count)` which generates a list of random numbers, the output would be consistent given the same seed value. For instance, calling `_subprocess_wrapper(generate_random_numbers, 42, [10])` might always return `[0.6394267984578837, 0.025010755222666936, 0.27502931836911926, 0.2232107582256786, 0.7364712141891934]` when the seed is set to `42`. This consistency is crucial for debugging and validating models or algorithms that rely on random processes.
## FunctionDef multiprocessing_wrapper(func_calls, n)
**multiprocessing_wrapper**: This function utilizes multiprocessing to execute a list of functions concurrently, each with its specified parameters. It is designed to enhance performance by parallelizing tasks when multiple subprocesses are required. If only one subprocess is needed (`n=1`), it will execute the functions sequentially without using multiprocessing.

parameters:
· func_calls: A list of tuples where each tuple contains a callable (function) and a tuple of arguments for that function.
· n: An integer representing the number of subprocesses to use. If `n` is 1, the function executes tasks sequentially.

Code Description: The `multiprocessing_wrapper` function first checks if the number of subprocesses (`n`) is 1 or if the adjusted number of processes (clamped between 1 and the length of `func_calls`) is also 1. In either case, it executes each function in `func_calls` sequentially using a list comprehension. Otherwise, it creates a multiprocessing pool with the specified number of subprocesses. For each function and its arguments in `func_calls`, it submits an asynchronous task to the pool using `_subprocess_wrapper`. This wrapper ensures that each subprocess starts with a fixed seed value generated by `LLM_CACHE_SEED_GEN.get_next_seed()`, which aids in reproducibility. After all tasks are submitted, it collects and returns their results.

Note: The function is particularly useful for parallelizing independent tasks to improve performance. It cooperates with the `chat_cache_seed` feature to ensure that random processes within subprocesses produce consistent results across different runs by using fixed seed values.

Output Example: Suppose `func_calls` contains two functions, each generating a list of 5 random numbers. If `n=2`, the function will execute these tasks in parallel, ensuring reproducibility due to fixed seeds. The output could be a list of lists like `[[0.6394267984578837, 0.025010755222666936, 0.27502931836911926, 0.2232107582256786, 0.7364712141891934], [0.123456789, 0.987654321, 0.543216789, 0.321654987, 0.789123456]]`, where each sublist corresponds to the output of one function call with a fixed seed.
## FunctionDef cache_with_pickle(hash_func, post_process_func)
**cache_with_pickle**: This function serves as a decorator to cache the return value of any given function using pickle serialization. It allows specifying a custom hash function to generate a unique key for caching purposes, and optionally, a post-processing function that can modify the cached result before it is returned.

parameters:
· hash_func: A callable that takes the same arguments as the decorated function and returns a string or None. This string serves as the cache key.
· post_process_func: An optional callable that processes the cached result before returning it to the caller. It should accept the original arguments, the cached result (as a keyword argument named 'cached_res'), and any additional keyword arguments.

Code Description: The function `cache_with_pickle` returns another function (`cache_decorator`) which in turn wraps the actual target function (`func`). When the wrapped function is called, it first checks if caching with pickle is enabled via RD_AGENT_SETTINGS.cache_with_pickle. If not, it simply calls the original function and returns its result.

If caching is enabled, `cache_with_pickle` constructs a cache directory path based on the module and name of the target function. It then generates a hash key using the provided `hash_func`. If the hash key is None, caching is bypassed for that particular call.

The function checks if a cached file exists for the generated hash key. If it does, the result is loaded from this file using pickle, optionally processed by `post_process_func`, and returned to the caller. If no cache file exists, the target function is executed, and its result is stored in a new cache file after serialization with pickle.

If RD_AGENT_SETTINGS.use_file_lock is set to True, file locking is used during the execution of the target function to prevent race conditions when multiple processes attempt to write to the same cache file simultaneously.

Note: Usage points include ensuring that `hash_func` returns consistent and unique keys for different sets of arguments to avoid incorrect caching behavior. The `post_process_func`, if provided, should be designed to handle the original arguments and the cached result correctly.

Output Example: Suppose we have a function `compute_expensive_operation` that performs some time-consuming computation based on its input parameters. By decorating it with `cache_with_pickle`, subsequent calls with the same parameters can return immediately from the cache instead of recomputing the result:

```python
def hash_func(x, y):
    return f"{x}-{y}"

@cache_with_pickle(hash_func)
def compute_expensive_operation(x, y):
    # Simulate an expensive operation
    import time
    time.sleep(5)  # Delay to mimic computation time
    return x * y

result1 = compute_expensive_operation(3, 4)  # Takes about 5 seconds
result2 = compute_expensive_operation(3, 4)  # Returns immediately from cache
```
### FunctionDef cache_decorator(func)
**cache_decorator**: This function is a decorator designed to cache the results of another function using pickle serialization. It checks if caching is enabled, computes a hash key for the function arguments, and uses this key to store or retrieve cached results from a file system.

parameters:
· func: The function whose results are to be cached. It should be callable.

Code Description: Detailed analysis and description.
The `cache_decorator` function takes another function as an argument and returns a new function (`cache_wrapper`) that wraps the original function with caching logic. Before executing the wrapped function, it checks if caching is enabled via `RD_AGENT_SETTINGS.cache_with_pickle`. If not, it simply calls the original function.

If caching is enabled, it constructs a target folder path based on the module name and function name of the wrapped function. This folder serves as the storage location for the cached results. The decorator then computes a hash key from the arguments passed to the function using `hash_func`. If the hash key computation fails (returns None), the original function is called without caching.

The cache file path is determined by appending the hash key to the target folder path with a `.pkl` extension, while the lock file path uses a similar structure but with a `.lock` extension. The decorator checks if a cache file already exists for the computed hash key. If it does, the cached result is loaded from this file using pickle and returned after optional post-processing by `post_process_func`.

If no cache file exists, the function executes the wrapped function to obtain the result. If file locking is enabled (`RD_AGENT_SETTINGS.use_file_lock`), a lock file is used to ensure that only one process can write to the cache at a time, preventing race conditions. After obtaining the result from the function call, it is serialized and written to the cache file for future use.

Note: Usage points.
This decorator is useful in scenarios where function calls are expensive (e.g., due to I/O operations or complex computations) and can be repeated with the same arguments. By caching results, subsequent calls with identical parameters can return immediately without re-executing the function, thus improving performance.

Output Example: Mock up a possible appearance of the code's return value.
Assuming `func` is a function that performs a computation and returns an integer, and given that caching is enabled and the result for specific arguments has been cached previously:

```python
result = cache_decorator(func)(arg1, arg2)
# If the result for (arg1, arg2) was already computed and stored in the cache,
# `result` will be the integer value loaded from the cache file.
```

In this example, if the function call with `(arg1, arg2)` has been executed before, `result` would hold the previously cached integer value. If not, the function would execute normally, compute the result, store it in the cache, and then return it.
#### FunctionDef cache_wrapper
**cache_wrapper**: This function serves as a decorator wrapper to cache the results of a function using pickle serialization. It checks if caching is enabled, computes a hash key for the function arguments, and uses this key to manage cached files. If a valid cache file exists, it loads the result from the file; otherwise, it executes the function, caches its result, and then returns the result.

**parameters**:
· *args: Any positional arguments that are passed to the function being decorated.
· **kwargs: Any keyword arguments that are passed to the function being decorated.

**Code Description**: The function begins by checking if caching is enabled via `RD_AGENT_SETTINGS.cache_with_pickle`. If not, it directly calls and returns the result of the function with the provided arguments. 

If caching is enabled, it constructs a target folder path based on the module name and function name where the cache files will be stored. It ensures that this directory exists by creating it if necessary.

A hash key for the current set of arguments is computed using `hash_func`. If the hash key is None, indicating an issue with argument hashing, the function bypasses caching and directly returns the result of the function call.

The function then constructs file paths for both the cache file (where the result will be stored) and a lock file (to manage concurrent access to the cache). If the cache file already exists, it loads the cached result from this file. If a post-processing function `post_process_func` is provided, it applies this function to the cached result before returning; otherwise, it returns the cached result directly.

If no valid cache file exists, the function checks if file locking is enabled via `RD_AGENT_SETTINGS.use_file_lock`. If so, it acquires a lock on the lock file before executing the function. This prevents race conditions when multiple processes attempt to write to the same cache file simultaneously. After obtaining the lock or directly if not using locks, it executes the function and stores its result.

The result of the function execution is then serialized using pickle and written to the cache file for future use. Finally, the function returns the result.

**Note**: This function assumes that `RD_AGENT_SETTINGS`, `func`, `hash_func`, and `post_process_func` are defined in the context where this decorator is used. The `FileLock` class must also be available for managing concurrent access to cache files when file locking is enabled.

**Output Example**: Assuming a function `compute_expensive_operation` decorated with `cache_wrapper` that takes two arguments, `a` and `b`, and returns their sum. If called with `compute_expensive_operation(3, 4)`, the first call would compute the result (7), cache it, and return 7. Subsequent calls with the same arguments would load the result from the cache file without recomputing, returning 7 immediately.
***
***
