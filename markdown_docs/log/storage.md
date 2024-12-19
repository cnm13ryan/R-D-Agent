## ClassDef FileStorage
**FileStorage**: The FileStorage class extends the Storage abstract base class to provide file-based logging functionality. It supports saving objects in JSON, text, or pickle formats and includes methods for iterating over logged messages and truncating logs based on a specified timestamp.

attributes:
· path: A Path object representing the directory where log files are stored.
· log_pattern: A compiled regular expression pattern used to parse log file content.

Code Description: The FileStorage class initializes with a specified path, creating the directory if it does not exist. The `log` method saves an object to a file within this directory, using a timestamp as part of the filename and formatting the file based on the specified save type (JSON, text, or pickle). It returns the path to the saved file.

The `iter_msg` method generates Message objects by iterating over all log files in the storage directory. It parses each file's content according to the log_pattern regular expression to extract timestamp, log level, caller information, and message content. For JSON and pickle files, it directly loads the serialized data into a Message object. The messages are sorted by timestamp before being yielded.

The `truncate` method removes any log entries later than a specified datetime from the storage directory. It reads each log file, filters out messages with timestamps after the given time, and writes the remaining content back to the file. If a message contains a reference to an external object (indicated by "Logging object in"), it deletes that object as well.

Note: Usage points include initializing FileStorage with a specific path for log storage, logging objects using different formats, iterating over stored messages, and truncating logs based on timestamps.

Output Example: When calling `log` with a string object and save_type 'text', the method might return a Path object pointing to a file like `/path/to/log/2023-10-05_14-30-00-123456.log`. The content of this file would be the string that was logged. When calling `iter_msg`, it could yield Message objects with attributes such as timestamp='2023-10-05 14:30:00.123456', level='INFO', caller='module.function:line_number', and content='Logged message'.
### FunctionDef __init__(self, path)
**__init__**: Initializes a new instance of the FileStorage class, setting up the directory path where log files will be stored.
parameters:
· path: A string or Path object representing the directory path where logs should be stored. Defaults to "./log/" if not provided.

Code Description: The __init__ method is the constructor for the FileStorage class. It takes one parameter, `path`, which can either be a string or an instance of the Path class from Python's pathlib module. This parameter specifies the directory path where log files will be stored. If no path is provided, it defaults to "./log/", indicating that logs should be stored in a folder named "log" within the current working directory.

Inside the method, the `path` parameter is converted into a Path object using `Path(path)`. This conversion ensures that all subsequent operations on this path can utilize the powerful methods and properties provided by the pathlib module. The `mkdir(parents=True, exist_ok=True)` method is then called on this Path object to create the directory specified by `path`, if it does not already exist. The `parents=True` argument allows for the creation of any necessary parent directories in the path, while `exist_ok=True` prevents an error from being raised if the directory already exists.

Note: Usage points include ensuring that the provided path is writable and has appropriate permissions to create files and directories. Developers should also be aware that using a relative path (like "./log/") will create the log directory relative to the current working directory of the script, which may vary depending on how the application is run. It's recommended to use absolute paths for more predictable behavior across different environments.
***
### FunctionDef log(self, obj, name, save_type, timestamp)
**log**: This function logs an object to a file system with a specified format and timestamp. It supports saving objects as JSON, text, or pickle files.

parameters:
· obj: The object to be logged.
· name: A string representing the name of the log file or directory path. If provided, it will be used to create subdirectories in the storage path.
· save_type: Specifies the format in which the object should be saved. It can be "json", "text", or "pkl". The default is "text".
· timestamp: An optional datetime object representing when the log entry was created. If not provided, the current UTC time will be used.
· **kwargs: Additional keyword arguments that are currently unused but allow for future expansion.

Code Description: Detailed analysis and description.
The function begins by checking if a timestamp has been provided; if not, it generates one using the current UTC time. The name parameter is processed to replace dots with slashes, which allows creating nested directories within the storage path based on the name. This directory structure is then created if it does not already exist.

A file path for the log entry is constructed by appending a formatted timestamp string to the directory path and adding an appropriate file extension based on the save_type parameter. The object is then serialized according to its type:
- If "json" is specified, the function attempts to serialize the object using json.dump(). If serialization fails due to a TypeError (e.g., if the object contains non-serializable elements), it converts the object to a string and tries again.
- For "pkl", the object is serialized using pickle.dump().
- When "text" is selected, the object is converted to a string and written directly to the file.

The function returns the path of the created log file as either a string or a Path object, depending on the underlying implementation details.

Note: Usage points.
This function is useful for logging data in various formats with timestamps, which can be helpful for debugging, monitoring, or archiving purposes. The ability to specify different save types makes it versatile for handling different kinds of data objects.

Output Example: Mock up a possible appearance of the code's return value.
'/path/to/storage/example/2023-10-05_14-30-00-123456.log'  # For text save_type
'/path/to/storage/example/2023-10-05_14-30-00-123456.json' # For json save_type
'/path/to/storage/example/2023-10-05_14-30-00-123456.pkl'  # For pkl save_type
***
### FunctionDef iter_msg(self, watch)
**iter_msg**: This function iterates over log messages stored in files within a specified directory structure. It reads both `.log` and `.pkl` files, parses the content to extract relevant information, and yields `Message` objects representing each log entry.

parameters:
· watch: A boolean flag indicating whether to continuously monitor for new log entries (default is False). Currently, this parameter does not affect the function's behavior as implemented.

Code Description: The function begins by initializing an empty list `msg_l` to store parsed `Message` objects. It then iterates over all `.log` files in the directory and its subdirectories using the `glob` method. For each file, it constructs a tag based on the file's relative path and extracts the process ID (pid) from the parent directory name.

The function reads the content of each `.log` file and uses a regular expression (`self.log_pattern`) to find all log entries within the file. It processes each match by extracting the timestamp, logging level, caller information, and message content. The timestamp is converted to a `datetime` object with UTC timezone information. If the message content contains "Logging object in", it skips that entry.

For `.pkl` files, the function follows a similar process but directly loads the content using `pickle.load`. It constructs a timestamp from the file's name and creates a `Message` object with an "INFO" level, as these files are assumed to contain serialized log data without detailed metadata.

After processing all files, the function sorts the list of `Message` objects by their timestamps in ascending order. Finally, it yields each message one by one, allowing the caller to iterate over them sequentially.

Note: Developers should ensure that log files and pickle files are correctly formatted and stored according to the expected structure for accurate parsing. Beginners might find it helpful to start with a simple directory containing `.log` files and gradually incorporate `.pkl` files as needed. The `watch` parameter is currently unused but could be implemented in future versions to support real-time log monitoring.
***
### FunctionDef truncate(self, time)
**truncate**: This function removes log entries from files located within a specified directory that have timestamps later than a given time.

**parameters**:
· time: A datetime object representing the cutoff point. Any log entry with a timestamp after this time will be removed.

**Code Description**: The `truncate` method iterates over all `.log` files in the directory and its subdirectories, identified by `self.path`. For each file, it reads the content and uses a regular expression defined by `self.log_pattern` to find log entries. Each log entry is expected to have a timestamp formatted as "%Y-%m-%d %H:%M:%S.%f" in UTC.

The method processes each log entry by extracting its timestamp and comparing it against the provided `time`. If the log entry's timestamp is later than `time`, the entry is skipped. Additionally, if the message part of the log entry contains "Logging object in", the function attempts to delete a file path specified in the message.

Log entries with timestamps earlier than or equal to `time` are retained and written back into the same file, effectively truncating the file to only include relevant logs.

**Note**: Usage points:
- Ensure that the directory structure and log files conform to the expected format for accurate processing.
- The function modifies files in place. It is advisable to backup important data before running this method.
- The regular expression `self.log_pattern` must correctly match the timestamp and message parts of each log entry for proper functionality.
- If a file path specified in a log message does not exist, an error will occur when attempting to delete it. Consider adding error handling if necessary.
***
