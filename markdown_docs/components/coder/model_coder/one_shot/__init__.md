## ClassDef ModelCodeWriter
**ModelCodeWriter**: This class is designed to generate model code based on a given experiment configuration. It inherits from the `Developer` class, specifically tailored for handling `ModelExperiment` objects. The primary function of this class is to develop and inject code into sub-tasks defined within an experiment.

attributes:
· exp: An instance of `ModelExperiment`, which contains details about the experiment including its sub-tasks.

Code Description: Detailed analysis and description.
The `ModelCodeWriter` class processes each sub-task within a provided `ModelExperiment`. For each sub-task, it initializes a `ModelFBWorkspace` object, prepares it, and then uses predefined prompts to generate code. The prompts are loaded from a YAML file located at the path specified by `DIRNAME / "prompt.yaml"`. These prompts are rendered with specific details about the sub-task such as its name, description, formulation, and variables.

The user prompt is designed to guide the generation of code for the model, while the system prompt provides general instructions. Both prompts are then used in conjunction with an API backend to create a chat completion request. The response from this request contains the generated Python code, which is extracted using a regular expression that looks for code enclosed within triple backticks.

The extracted code is then injected into the `ModelFBWorkspace` object under the filename "model.py". Each processed sub-task's workspace is appended to a list, which is finally assigned to the experiment's `sub_workspace_list`. The modified `ModelExperiment` object, now containing the generated code for each of its sub-tasks, is returned.

Note: Usage points.
This class is particularly useful in scenarios where automated code generation based on experimental configurations is required. It leverages prompt-based AI models to generate model implementations efficiently. Developers can use this class by creating an instance of `ModelExperiment`, configuring it with the necessary details for each sub-task, and then passing this experiment object to an instance of `ModelCodeWriter`.

Output Example: Mock up a possible appearance of the code's return value.
Assuming we have an experiment with one sub-task configured as follows:
- Name: Linear Regression Model
- Description: A simple linear regression model to predict housing prices based on square footage.
- Formulation: Predict price = intercept + slope * square_footage
- Variables: square_footage (independent), price (dependent)

The `ModelCodeWriter` would generate a Python script similar to the following and inject it into the sub-task's workspace:

```python
# model.py

class LinearRegressionModel:
    def __init__(self):
        self.intercept = 0.0
        self.slope = 0.0

    def fit(self, square_footage, price):
        # Implementation of fitting the model to data
        pass

    def predict(self, square_footage):
        return self.intercept + self.slope * square_footage
```

The `ModelExperiment` object returned by `ModelCodeWriter` would then contain this generated code within its sub-task's workspace.
### FunctionDef develop(self, exp)
**develop**: This function processes a given ModelExperiment by iterating through its sub-tasks, preparing a model feedback workspace for each task, generating prompts based on predefined templates, sending these prompts to an API backend to receive code implementations, and then injecting the generated code into the respective workspaces.

**parameters**:
· exp: An instance of ModelExperiment that contains sub-tasks which need to be processed. Each sub-task includes details such as name, description, formulation, and variables necessary for generating the corresponding code.

**Code Description**: The function starts by initializing an empty list `mti_l` to store instances of ModelFBWorkspace. It then iterates over each task (`t`) in the `exp.sub_tasks`. For each task, it creates a new instance of ModelFBWorkspace named `mti`, prepares this workspace, and loads prompts from a YAML file located at `DIRNAME / "prompt.yaml"`. The prompts are templated using Jinja2's Environment with StrictUndefined to ensure that all placeholders in the templates must be provided.

The user prompt is rendered by filling in the template with the task's name, description, formulation, and variables. The system prompt is also rendered but does not require any additional context. These prompts are then sent to an API backend through the `APIBackend().build_messages_and_create_chat_completion` method to generate a response.

The function uses a regular expression to extract the Python code from the API's response, which is expected to be enclosed within triple backticks followed by 'python'. This extracted code is then injected into the model feedback workspace (`mti`) under the filename "model.py". After processing all sub-tasks, the list of workspaces `mti_l` is assigned to `exp.sub_workspace_list`, and the modified `exp` object is returned.

**Note**: The function assumes that the API response will contain valid Python code enclosed in triple backticks. It also relies on the presence of specific keys ("code_implement_user", "code_implement_sys") in the loaded prompts YAML file.

**Output Example**: Assuming an experiment with one sub-task named "Simple Addition" that requires implementing a function to add two numbers, the output might look like this:

```
ModelExperiment(
    sub_tasks=[
        SubTask(
            name="Simple Addition",
            description="Implement a function to add two numbers.",
            formulation="Create a Python function `add_numbers(a, b)` that returns the sum of `a` and `b`.",
            variables=["a", "b"]
        )
    ],
    sub_workspace_list=[
        ModelFBWorkspace(
            task=SubTask(...),
            injected_code={
                "model.py": "def add_numbers(a, b):\n    return a + b"
            }
        )
    ]
)
```
***
