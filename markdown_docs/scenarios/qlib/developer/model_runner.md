## ClassDef QlibModelRunner
**QlibModelRunner**: This class extends CachedRunner to manage and execute Qlib model experiments within a Docker environment. It handles configurations, injects necessary code into the experiment workspace, sets up the runtime environment based on the model type, executes the experiment, and stores the results.

attributes:
· exp: An instance of QlibModelExperiment which contains all necessary information about the experiment including sub-workspaces, tasks, and configurations.

Code Description: The develop method is decorated with cache_with_pickle to ensure that once an experiment is executed, its result can be cached for future reference without re-execution. It first checks if 'model.py' exists in the code dictionary of the first sub-workspace. If not, it raises a ModelEmptyError indicating that no model file was provided.

If 'model.py' is present, the method injects this code into the experiment workspace. The environment variables are then set up with PYTHONPATH pointing to the current directory. Depending on whether the model type specified in the first sub-task is "TimeSeries" or "Tabular", additional environment variables such as dataset_cls, step_len, and num_timesteps are added.

The experiment is executed using the qlib_config_name 'conf.yaml' and the prepared run_env dictionary containing all necessary environment settings. The result of this execution is stored in the exp.result attribute and then returned.

Note: This class assumes that a Docker container with Qlib installed is available, and it relies on configurations defined in 'config.yaml', 'model.py', and 'conf.yaml'. It also requires that the model specified in 'model.py' matches the expected format (e.g., 'Net' for PyTorch models).

Output Example: The output of the develop method would be an instance of QlibModelExperiment with its result attribute populated. This result could include metrics, logs, and other data generated during the execution of the model experiment within the Docker environment.

Example:
{
    "experiment_id": "12345",
    "model_type": "TimeSeries",
    "result": {
        "train_loss": 0.01,
        "validation_loss": 0.02,
        "metrics": {"accuracy": 0.98, "precision": 0.97},
        "logs": ["Epoch 1: loss=0.03", "Epoch 2: loss=0.02"]
    }
}
### FunctionDef develop(self, exp)
**develop**: This function takes a QlibModelExperiment object as input, checks if the 'model.py' file is present and not empty, injects the model code into the experiment workspace, sets up the appropriate environment variables based on the model type (TimeSeries or Tabular), executes the experiment, stores the result in the experiment object, and returns the updated QlibModelExperiment object.

**parameters**:
· exp: An instance of QlibModelExperiment which contains details about the experiment setup including sub-workspaces, tasks, and configurations.

**Code Description**: The function starts by verifying if 'model.py' exists within the first sub-workspace's code dictionary. If it does not exist or is empty, a ModelEmptyError is raised indicating that 'model.py' is missing. Assuming 'model.py' is present and contains valid code, this code is then injected into the experiment workspace using the `inject_code` method.

Next, an environment dictionary (`env_to_use`) is initialized with a default PYTHONPATH set to './'. Depending on the model type specified in the first sub-task of the experiment (either 'TimeSeries' or 'Tabular'), additional environment variables are added. For TimeSeries models, it includes 'dataset_cls', 'step_len', and 'num_timesteps'; for Tabular models, only 'dataset_cls'.

The `execute` method of the experiment workspace is then called with a configuration file name ('conf.yaml') and the constructed environment dictionary. The result of this execution is stored in the `result` attribute of the QlibModelExperiment object.

Finally, the function returns the updated QlibModelExperiment object which now includes the results from the executed model.

**Note**: It is crucial that 'model.py' contains valid code compatible with the experiment setup and that the configuration file ('conf.yaml') exists in the expected location. The environment variables set must align with the requirements of the model being developed.

**Output Example**: Assuming the execution was successful, the returned QlibModelExperiment object might look something like this:

```
QlibModelExperiment(
    sub_workspace_list=[SubWorkspace(code_dict={'model.py': '...'})],
    sub_tasks=[SubTask(model_type='TimeSeries')],
    experiment_workspace=ExperimentWorkspace(),
    result={
        'train_loss': 0.1234,
        'val_loss': 0.1567,
        'test_accuracy': 0.9876
    }
)
```

This example illustrates that the `result` attribute of the QlibModelExperiment object contains metrics such as training and validation loss, along with test accuracy, which are typical outputs from a model execution process.
***
