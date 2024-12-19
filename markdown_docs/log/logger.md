## ClassDef RDAgentLog
**RDAgentLog**: RDAgentLog is a class designed to manage logging within an application. It organizes log files based on tags and process IDs (PID). This class ensures that logs are stored in a structured manner, making it easier to trace and debug issues.

**attributes**:
· _tag: A private string attribute used internally to store the current tag for log entries.
· log_trace_path: The path where log files will be stored. If not specified during initialization, a default path is created using the current working directory and a timestamp.
· storage: An instance of FileStorage that handles file operations related to logging.
· main_pid: Stores the process ID of the main process.

**Code Description**: RDAgentLog inherits from SingletonBaseClass, ensuring only one instance of the logger exists throughout the application. The constructor initializes the log path and creates necessary directories if they do not exist. It also sets up a FileStorage object for handling file operations. The `set_trace_path` method allows changing the log trace path dynamically.

The `tag` context manager is used to set a tag for log entries, which helps in categorizing logs based on specific contexts or modules within the application. This method ensures that tags are properly nested and can be easily managed even when dealing with multiple logging calls.

The `get_pids` method returns a string of process IDs from the current process back to the main process, separated by hyphens. This information is useful for tracing logs across different processes or threads.

The `file_format` method defines how log records are formatted before being written to files. It includes options for raw and formatted messages, with ANSI codes removed from messages in both cases.

The `log_object` method allows logging of arbitrary objects by serializing them into a file using pickle format. This is useful for storing complex data structures that cannot be easily represented as strings. The method also logs the path to the serialized object in the common_logs.log file.

The `info`, `warning`, and `error` methods are used to log messages at different severity levels (informational, warning, and error). These methods handle formatting, adding caller information, and writing messages to the appropriate log files. They also manage logger handlers to ensure that logs are written correctly without interfering with other logging operations.

**Note**: RDAgentLog is designed to be used as a singleton instance throughout an application. It provides structured logging capabilities that can be easily integrated into various parts of an application, helping in maintaining clear and organized log files.

**Output Example**: When calling `info("This is a test message.", tag="test")` from a process with PID 12345, the following entry might appear in the log file located at `log_trace_path/test.12345/common_logs.log`:

```
2023-10-05 14:23:45.678 | INFO     | module_name:function_name:line_number - This is a test message.
```
### FunctionDef __init__(self, log_trace_path)
**__init__**: Initializes an instance of RDAgentLog, setting up the log trace path and preparing storage for logging.

parameters:
· log_trace_path: A string or Path object specifying the directory where logs will be stored. If None, a default path is generated using the current working directory and a timestamp.

Code Description: The constructor first checks if `log_trace_path` is provided. If not, it generates a unique timestamp to create a new directory for logging under the "log" folder in the current working directory. This ensures that each instance of RDAgentLog has its own log storage area, preventing conflicts between multiple instances.

Once the log path is determined, the constructor creates the necessary directories if they do not already exist using `mkdir(parents=True, exist_ok=True)`. This step guarantees that the specified logging path is ready for use without raising an error if the directory structure already exists.

Next, it initializes a FileStorage object with the determined log trace path. The FileStorage class handles the actual storage and retrieval of log data in files, providing methods to log objects, iterate over stored messages, and truncate logs based on timestamps.

Finally, the constructor captures the main process ID (PID) using `os.getpid()` and stores it in the instance variable `main_pid`. This PID can be useful for identifying which process generated specific log entries, especially in multi-process applications.

Note: Usage points include initializing RDAgentLog with a custom path for log storage or relying on the default timestamp-based directory creation. Once initialized, developers can use the methods provided by the FileStorage object to manage and retrieve logs effectively.
***
### FunctionDef set_trace_path(self, log_trace_path)
**set_trace_path**: This function configures the logging system by setting a specific path where log files will be stored. It initializes a `FileStorage` object to handle file-based logging operations at the specified location.

parameters:
· log_trace_path: A string or Path object representing the directory where log files should be stored. This parameter determines the root directory for all logging activities managed by this instance of RDAgentLog.

Code Description: The function takes a single argument, `log_trace_path`, which can be either a string or a Path object. It converts this input into a Path object and assigns it to the instance variable `self.log_trace_path`. This step ensures that the path is consistently handled as a Path object throughout the class methods.

Following the assignment of `self.log_trace_path`, the function creates an instance of `FileStorage` with the same path. The `FileStorage` object, stored in `self.storage`, will be used to perform file-based logging operations such as saving log messages, iterating over existing logs, and truncating old entries based on timestamps.

By setting the trace path, developers can specify where all log files should reside, making it easier to manage and locate logs for debugging or monitoring purposes. This function is crucial for initializing the logging system with a designated storage location before any logging activities take place.

Note: Usage points include calling `set_trace_path` with a valid directory path before performing any logging operations. This ensures that all log files are stored in the specified location, facilitating organized and accessible logging. For example, setting the trace path to `/var/log/myapp/` would store all logs within this directory structure.
***
### FunctionDef tag(self, tag)
**tag**: This function allows adding a tag to an internal logging mechanism, facilitating more granular control over log messages by appending tags dynamically. It is designed to be used within a context manager to ensure that the tag is added before some operations and removed afterward.

parameters:
· tag: A string representing the tag to be added. The tag cannot be empty; otherwise, a ValueError will be raised.

Code Description: The function `tag` takes a single parameter, `tag`, which must be a non-empty string. It first checks if the provided tag is empty or consists only of whitespace characters and raises a `ValueError` if this condition is met. If there is already an existing tag stored in `_tag`, it prepends a dot to the new tag before appending it to `_tag`. This behavior ensures that multiple tags are concatenated with dots, creating a structured format.

The function then enters a try block where it yields control back to the caller, allowing any code within the context manager to execute. After this point, regardless of whether an exception was raised or not, the `finally` block is executed. In this block, the function removes the recently added tag from `_tag`, restoring its previous state before the function was called.

Note: Usage points include using this function within a with statement to temporarily add a tag for logging purposes during specific operations. This can be particularly useful in complex applications where distinguishing between different parts of the code or different types of log messages is necessary. However, developers should be cautious about potential issues in multithreaded environments or when used in coroutines, as the current implementation does not account for concurrent modifications to `_tag`.
***
### FunctionDef get_pids(self)
**get_pids**: Returns a string of process IDs (PIDs) from the current process to the main process, separated by hyphens.
parameters:
· None: This function does not accept any parameters.

Code Description: The `get_pids` method is designed to trace back from the current process to the main process and compile a chain of PIDs. It starts by obtaining the PID of the current process using `os.getpid()`. A `Process` object is then created with this PID. The function initializes a string, `pid_chain`, with the current PID. In a loop that continues until the current process's PID matches the main process's PID (`self.main_pid`), it retrieves the parent process ID (`ppid`) of the current process using the `ppid()` method of the `Process` object. A new `Process` object is created for this parent PID, and its value is prepended to `pid_chain`, separated by a hyphen. This loop effectively builds a chain of PIDs from the current process back to the main process.

Note: The function assumes that `self.main_pid` is defined elsewhere in the class and represents the PID of the main process. It also relies on the `Process` class, which should be imported or defined within the same module or package.

Output Example: If the current process has a PID of 12345, its parent process has a PID of 6789, and this is the main process, the function would return "6789-12345".
***
### FunctionDef file_format(self, record, raw)
**file_format**: This function formats log records into a string representation suitable for file storage or display. It optionally strips ANSI color codes from the message and can output either a raw message or a formatted one with timestamps, log levels, and other contextual information.

parameters:
· record: A dictionary representing a log record that includes at least a "message" key.
· raw: A boolean flag indicating whether to return a raw message without additional formatting. Defaults to False.

Code Description: The function begins by removing any ANSI color codes from the "message" field of the log record using the `remove_ansi_codes` method from the `LogColors` class. This ensures that the log messages stored in files do not contain terminal-specific formatting, which could be problematic for environments that do not support ANSI escape sequences.

If the `raw` parameter is set to True, the function returns only the message part of the log record, formatted as "{message}". Otherwise, it constructs a more detailed string that includes the timestamp, log level, logger name, function name, line number, and the cleaned-up message. The format for this detailed output is specified as "{time:YYYY-MM-DD HH:mm:ss.SSS} | {level: <8} | {name}:{function}:{line} - {message}\n", where:
- `time` is formatted to show year, month, day, hour, minute, second, and millisecond.
- `level` represents the severity level of the log message (e.g., INFO, WARNING, ERROR) and is left-aligned within an 8-character wide field.
- `name`, `function`, and `line` provide context about where in the code the log was generated.

Note: The function's format string is tightly coupled with how messages are read from storage, which may require adjustments if the message reading logic changes. Additionally, the use of ANSI escape sequences for coloring terminal output is handled elsewhere, and this function focuses on preparing log messages for file storage or display in environments that do not support such formatting.

Output Example: Given a log record `{"message": "\033[91mError occurred\033[0m", "level": "ERROR", "name": "my_logger", "function": "process_data", "line": 42, "time": datetime.datetime(2023, 10, 5, 14, 30, 0, 123456)}`, the function would return:
- If `raw` is True: `"Error occurred"`
- If `raw` is False: `"2023-10-05 14:30:00.123 | ERROR    | my_logger:process_data:42 - Error occurred\n"`
***
### FunctionDef log_object(self, obj)
**log_object**: Logs a given object to storage with optional tagging and caller information.
parameters:
· obj: The object to be logged. This can be any Python object that is serializable.
· tag: An optional string used to categorize or identify the log entry. Defaults to an empty string.

Code Description: The `log_object` function is designed to serialize and store a given object, along with contextual information such as caller details and process IDs. It begins by retrieving caller information using the `get_caller_info` function, which provides details about the module name, line number, and function name from which the call was made.

The tag for the log entry is constructed by combining the logger's existing tag (self._tag), any additional tag provided as an argument, and a chain of process IDs obtained from `get_pids`. This ensures that each log entry can be uniquely identified and traced back to its origin within the application.

The object is then logged using the `storage.log` method with the constructed tag and a specified save type ("pkl" for pickle). The path where the object is stored (`logp`) is used in subsequent logging operations.

A file handler is added to log messages to a specific file path, which is derived from the tag. This file path includes directories corresponding to each segment of the tag, ensuring that logs are organized hierarchically. The `file_format` method is used to format these log messages according to a predefined structure, which includes timestamps, log levels, and caller information.

The logger's patch method is used to temporarily update the record with caller information before logging an informational message indicating where the object has been logged. After logging, the file handler is removed to clean up resources.

Note: This function is particularly useful for debugging and monitoring applications by providing detailed logs of objects at various points in the code. It allows developers to track changes to objects over time and understand their context within the application's execution flow. The use of tags and process IDs helps in organizing and filtering log data effectively.
***
### FunctionDef info(self, msg)
**info**: Logs an informational message to a specified log file and optionally outputs it to standard error. It allows tagging messages and choosing between raw and formatted output.

**parameters**:
· msg: A string representing the informational message to be logged.
· tag: An optional string used to categorize or identify the log entry. Defaults to an empty string.
· raw: A boolean flag indicating whether to log a raw message without additional formatting. Defaults to False.

Code Description: The `info` method is designed to handle logging of informational messages with flexibility in output format and destination. It begins by retrieving caller information using the `get_caller_info` function, which provides details about where the log call originated from within the application.

The method constructs a tag for the log entry by combining the instance's predefined tag, any additional tag provided as an argument, and a chain of process IDs (PIDs) obtained through the `get_pids` method. This tag is used to determine the path where the log file will be stored, ensuring that logs are organized based on their origin and context.

If the `raw` parameter is set to True, the logger configuration is temporarily modified to output only the raw message to standard error (stderr). This involves removing any existing handlers from the logger and adding a new handler that outputs messages directly without formatting. After logging the message, the logger's configuration is restored to its original state.

If `raw` is False, the method adds a file handler to the logger configured with a detailed format specified by the `file_format` method. This format includes timestamps, log levels, and other contextual information about where the log was generated. The message is then logged using this formatted configuration.

After logging the message, the newly added file handler is removed from the logger to prevent it from affecting subsequent log entries. If the `raw` parameter was True, the logger's handlers are reset again to ensure that standard error output does not persist beyond the scope of this method call.

Note: This method provides a flexible way to log informational messages with detailed context and allows for both formatted and raw outputs. It is particularly useful in applications where logs need to be categorized and stored in specific directories based on their origin, while also providing an option for quick debugging output through standard error. Developers can use this method to add informative logging throughout their application, aiding in both development and production monitoring. Beginners should ensure they understand how tags and raw outputs work to effectively utilize the logging capabilities provided by this method.
***
### FunctionDef warning(self, msg)
**warning**: Logs a warning message with optional tagging and caller information.
parameters:
· msg: A string representing the warning message to be logged.
· tag: An optional string used to categorize or identify the log entry. Defaults to an empty string.

Code Description: The `warning` method is designed to handle logging of warning messages within an application, providing detailed context about where and why the warning was issued. It begins by retrieving caller information using the `get_caller_info` function, which captures details such as the module name, line number, and function name from which the call was made.

The method constructs a tag for the log entry by combining the instance's predefined `_tag`, any additional `tag` provided as an argument, and a chain of process IDs (PIDs) obtained from the `get_pids` method. This PID chain helps trace the warning back to its origin within the application's process hierarchy.

A file handler is then added to the logger for writing the log message to a specific file path derived from the constructed tag. The file path is structured as `log_trace_path / tag.replace(".", "/") / "common_logs.log"`, ensuring that logs are organized in a directory structure that reflects their tags and origins.

The warning message is formatted according to the instance's `file_format` method, which ensures consistency across log entries. This formatting includes timestamps, log levels, logger names, function names, line numbers, and the cleaned-up message (stripped of ANSI color codes).

After logging the warning message with the caller information, the file handler is removed to clean up resources and prevent potential memory leaks or performance issues.

Note: Usage points include scenarios where developers need to issue warnings about non-critical issues that may require attention but do not halt program execution. The method provides a structured way to log these warnings, making it easier for developers to diagnose and address issues based on the detailed context provided in each log entry.
***
### FunctionDef error(self, msg)
**error**: Logs an error message with optional tagging and caller information.
parameters:
· msg: A string representing the error message to be logged.
· tag: An optional string used to categorize or identify the log entry. Defaults to an empty string.

Code Description: The `error` method is designed to handle logging of error messages within a structured format, incorporating additional context for debugging and tracking purposes. Upon invocation, it first retrieves caller information using the `get_caller_info` function, which includes details such as the module name, line number, and function name from where the call was made.

The method constructs a tag by concatenating the instance's predefined `_tag`, any user-provided `tag`, and the process ID chain obtained from the `get_pids` method. This tag is used to create a unique file path for storing the log entry, ensuring that logs are organized and easily traceable.

A new file handler is added to the logger using the constructed file path, with the log format specified by the instance's `file_format`. The error message is then logged with the caller information patched into each record. This ensures that every log entry includes detailed context about its origin, which can be invaluable for debugging and monitoring.

After logging the error message, the file handler is removed to clean up resources and prevent potential memory leaks or excessive file descriptors from being used.

Note: The `error` method is particularly useful in scenarios where precise tracking of errors is necessary. By incorporating caller information and a structured tagging system, it provides developers with comprehensive insights into when and where errors occur within an application. This can significantly enhance the debugging process and help maintain the stability and reliability of software systems.
***
