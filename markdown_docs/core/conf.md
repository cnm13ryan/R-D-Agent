## ClassDef ExtendedEnvSettingsSource
**ExtendedEnvSettingsSource**: This class extends the functionality of an environment settings source by dynamically gathering prefixes from both the current and parent classes to retrieve field values.

attributes:
· field: An instance of FieldInfo representing the field for which a value is being retrieved.
· field_name: A string representing the name of the field.

Code Description: The ExtendedEnvSettingsSource class inherits from EnvSettingsSource. It overrides the get_field_value method to enhance how environment variable values are fetched based on dynamically determined prefixes. Initially, it collects prefixes starting with the prefix defined in its own configuration or defaults to an empty string if not specified. If the settings class has parent classes (indicated by __bases__), it further checks each parent for a model_config attribute containing an env_prefix. If such a prefix exists and hasn't been added yet, it is appended to the list of prefixes.

The method then iterates over all collected prefixes, setting the current prefix in self.env_prefix before calling the superclass's get_field_value method with the provided field and field_name. If this call returns a non-None value for env_val (indicating that an environment variable was found), it immediately returns this value along with the corresponding field key and a boolean indicating whether the value is complex.

If no valid environment variable is found after checking all prefixes, it defaults to calling the superclass's get_field_value method without any prefix adjustments.

Note: This class is utilized in the settings_customise_sources method of ExtendedBaseSettings, where an instance of ExtendedEnvSettingsSource is returned as part of a tuple defining custom sources for settings. This allows the application to use environment variables with multiple prefixes when loading configuration settings.

Output Example: Suppose there are two classes, ChildConfig and ParentConfig, each with different env_prefix values ('CHILD_' and 'PARENT_', respectively). When ExtendedEnvSettingsSource is used to fetch a value for a field named 'API_KEY', it will first look for an environment variable named 'CHILD_API_KEY'. If not found, it will then check for 'PARENT_API_KEY'. If neither is found, it will revert to the default behavior of EnvSettingsSource. The method returns a tuple containing the value (or None if no valid environment variable was found), the field key used, and a boolean indicating whether the value is complex.
### FunctionDef get_field_value(self, field, field_name)
**get_field_value**: This function retrieves the value of a specified field from environment variables, considering potential prefixes defined in the configuration and parent classes.

parameters:
· field: An instance of FieldInfo representing the field for which the value is being retrieved.
· field_name: A string that specifies the name of the field.

Code Description: The function starts by collecting all relevant environment variable prefixes. It first checks if there is a prefix defined in the current class's configuration and adds it to the list of prefixes. Then, it examines each parent class (if any) to see if they have their own model configurations with an 'env_prefix'. If such a prefix exists and hasn't been added yet, it gets included in the list.

For each collected prefix, the function sets the environment variable prefix for the current instance and calls the superclass's `get_field_value` method. This method is responsible for fetching the actual value of the field from the environment variables using the current prefix. If a non-None value is returned (indicating that the field was found in an environment variable), the function immediately returns this value along with the corresponding field key and a boolean indicating whether the value is complex.

If no value is found after checking all prefixes, the function defaults to calling the superclass's `get_field_value` method without any prefix. This ensures that even if no specific prefix matches, the function can still attempt to retrieve the field value directly from the environment variables.

Note: Usage points include scenarios where configurations are spread across multiple classes with different prefixes or when a fallback mechanism is needed for fields not prefixed in environment variables.

Output Example: If the function successfully retrieves a value for a field named 'API_KEY' with a prefix 'DEV_', it might return something like ('abc123', 'DEV_API_KEY', False), where 'abc123' is the retrieved value, 'DEV_API_KEY' is the key used in the environment variable, and False indicates that the value is not complex.
***
## ClassDef ExtendedSettingsConfigDict
**ExtendedSettingsConfigDict**: This class extends the functionality of `SettingsConfigDict` by allowing a configuration dictionary to be defined with optional keys, indicated by the `total=False` parameter.

attributes:
· total: A boolean flag that indicates whether all fields defined in the model are required (True) or if some can be omitted (False). Here, it is set to False, meaning not all keys need to be present in an instance of this class.

Code Description: The `ExtendedSettingsConfigDict` class inherits from `SettingsConfigDict`, which likely provides a base structure for configuration settings. By setting `total=False`, the new class allows for flexibility in the configuration dictionary, where only some of the defined keys are mandatory. This is particularly useful when dealing with configurations that may have optional parameters or when different environments require different subsets of settings.

Note: Usage points include scenarios where configuration settings might vary between development, testing, and production environments, or when certain features are enabled or disabled based on user preferences or system capabilities. Developers can leverage this class to define a comprehensive set of possible configuration options while ensuring that not all configurations need to be provided in every context. This approach enhances the adaptability and maintainability of the application's settings management.
## ClassDef ExtendedBaseSettings
**ExtendedBaseSettings**: This class extends BaseSettings from Pydantic and customizes how settings sources are loaded by overriding the `settings_customise_sources` method.

attributes:
· settings_cls: The class of the settings being configured, which is a subclass of BaseSettings.
· init_settings: A source for settings that comes from the initialization parameters.
· env_settings: A source for settings that comes from environment variables.
· dotenv_settings: A source for settings that comes from .env files.
· file_secret_settings: A source for settings that comes from secret files.

Code Description: The `ExtendedBaseSettings` class inherits from Pydantic's BaseSettings. It overrides the `settings_customise_sources` method to specify a custom order and type of sources from which settings should be loaded. In this case, it returns a tuple containing only one source, `ExtendedEnvSettingsSource`, indicating that all settings will be sourced exclusively from an environment-specific settings source defined elsewhere in the project.

Note: This customization is useful when developers want to enforce or prioritize certain types of configuration sources over others. By returning only `ExtendedEnvSettingsSource`, it ensures that no other default sources like initialization parameters, environment variables, .env files, or secret files are considered for loading settings.

Output Example: The method would return a tuple containing an instance of `ExtendedEnvSettingsSource` configured with the class type passed as `settings_cls`. This might look something like this in practice:

```
(ExtendedEnvSettingsSource(<class 'core.conf.RDAgentSettings'>),)
``` 

This output indicates that for any subclass of `ExtendedBaseSettings`, such as `RDAgentSettings`, the settings will be loaded exclusively from the custom environment source defined by `ExtendedEnvSettingsSource`.
### FunctionDef settings_customise_sources(cls, settings_cls, init_settings, env_settings, dotenv_settings, file_secret_settings)
**settings_customise_sources**: This function customizes the sources from which settings are loaded by returning a tuple of setting sources. It specifically replaces the default environment settings source with an instance of ExtendedEnvSettingsSource, which enhances how environment variables are retrieved using dynamic prefixes.

parameters:
· cls: The class on which this method is called.
· settings_cls: A type hint indicating that the parameter should be a subclass of BaseSettings, representing the configuration class for which settings sources are being customized.
· init_settings: An instance of PydanticBaseSettingsSource used to load settings from initialization parameters. This parameter is included in the function signature but not utilized within the function body.
· env_settings: An instance of PydanticBaseSettingsSource used to load settings from environment variables. This parameter is also included in the function signature but not utilized within the function body.
· dotenv_settings: An instance of PydanticBaseSettingsSource used to load settings from .env files. This parameter is included in the function signature but not utilized within the function body.
· file_secret_settings: An instance of PydanticBaseSettingsSource used to load sensitive settings from a secrets file. This parameter is included in the function signature but not utilized within the function body.

Code Description: The function settings_customise_sources is designed to modify the sources from which configuration settings are loaded for a given settings class (settings_cls). It accepts several parameters that represent different types of setting sources, such as initialization parameters, environment variables, .env files, and secrets files. However, within the function body, only the settings_cls parameter is used.

The function creates an instance of ExtendedEnvSettingsSource, passing the settings_cls to its constructor. This custom source enhances the retrieval of environment variable values by dynamically determining prefixes based on both the current and parent classes' configurations. The function then returns a tuple containing this new source as the sole element.

Note: By returning an instance of ExtendedEnvSettingsSource, this method allows the application to use multiple environment variable prefixes when loading configuration settings, making it more flexible in environments where different configurations might have distinct naming conventions for their environment variables.

Output Example: Suppose there is a settings class named AppConfig with an env_prefix of 'APP_', and its parent class BaseConfig has an env_prefix of 'BASE_'. When settings_customise_sources is called to customize the sources for AppConfig, it returns a tuple containing an instance of ExtendedEnvSettingsSource. This source will first look for environment variables prefixed with 'APP_' when fetching values for fields in AppConfig. If no such variable is found, it will then check for variables prefixed with 'BASE_'. If neither prefix yields a valid value, it defaults to the standard behavior of EnvSettingsSource without any prefix adjustments. The method returns a tuple containing this custom source, which is used by Pydantic to load settings for AppConfig.
***
## ClassDef RDAgentSettings
**RDAgentSettings**: This class extends ExtendedBaseSettings to define configuration settings specific to an RD Agent. It includes various configurations related to logging, Azure Document Intelligence, factor extraction, workspace management, multiprocessing, and caching mechanisms.

attributes:
· log_trace_path: A string or None indicating the path where trace logs should be stored. If not specified (None), no trace logging will occur.
· azure_document_intelligence_key: A string representing the API key for accessing Azure Document Intelligence services.
· azure_document_intelligence_endpoint: A string representing the endpoint URL for Azure Document Intelligence services.
· max_input_duplicate_factor_group: An integer specifying the maximum number of duplicate factor groups allowed in input data, defaulting to 300.
· max_output_duplicate_factor_group: An integer specifying the maximum number of duplicate factor groups allowed in output data, defaulting to 20.
· max_kmeans_group_number: An integer defining the maximum number of clusters for K-means clustering, set to 40 by default.
· workspace_path: A Path object indicating the directory path where the RD Agent's workspace is located. By default, it points to a subdirectory named "RD-Agent_workspace" within a folder called "git_ignore_folder" in the current working directory.
· multi_proc_n: An integer representing the number of processes to use for multiprocessing tasks, initialized to 1.
· cache_with_pickle: A boolean flag indicating whether to use pickle caching. It is set to True by default.
· pickle_cache_folder_path_str: A string specifying the path to the folder where pickle caches are stored. By default, it points to a subdirectory named "pickle_cache" in the current working directory.
· use_file_lock: A boolean flag determining whether to use file locks when executing functions with identical parameters to prevent multiple executions simultaneously. It is set to True by default.

Code Description: The RDAgentSettings class inherits from ExtendedBaseSettings, which customizes how settings are loaded by overriding the `settings_customise_sources` method. This ensures that all configuration values for an instance of RDAgentSettings are sourced exclusively through a custom environment-specific source defined elsewhere in the project. The attributes of RDAgentSettings represent various configurable aspects of the RD Agent's operation, including logging paths, service credentials, processing limits, workspace locations, multiprocessing configurations, and caching mechanisms.

Note: Developers should ensure that sensitive information such as API keys (e.g., azure_document_intelligence_key) is securely managed and not hard-coded into the source code. The use of environment variables or secure vaults is recommended for storing such data. Additionally, when configuring paths like workspace_path and pickle_cache_folder_path_str, developers must consider file system permissions to ensure that the RD Agent can read from and write to these locations without issues.
