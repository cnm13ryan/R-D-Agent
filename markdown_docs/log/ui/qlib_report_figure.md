## ClassDef BaseGraph
**BaseGraph**: The BaseGraph class serves as a foundational structure for creating graphical representations of data using Plotly. It initializes with a DataFrame, layout settings, graph parameters, and name mappings, preparing these elements to generate visual plots.

attributes:
· df: A pandas DataFrame containing the dataset to be visualized.
· layout: A dictionary specifying layout parameters for the plot, such as title, axis labels, etc.
· graph_kwargs: A dictionary holding additional keyword arguments that are specific to the type of graph being created (e.g., bar chart, scatter plot).
· name_dict: A dictionary mapping column names in the DataFrame to more user-friendly display names.

Code Description: The BaseGraph class is designed to encapsulate the creation and configuration of a Plotly figure. Upon initialization, it accepts several parameters that define how the data should be visualized. It checks if the provided DataFrame is empty and raises an error if so. The `_init_parameters` method sets up default values for graph type and column names based on the input or defaults to the original column names in the DataFrame.

The `_get_data` method constructs a list of Plotly graph objects, each representing a trace of data from the DataFrame. This is done by iterating over columns specified in `name_dict`, creating a graph object for each with parameters defined in `graph_kwargs`.

The `_get_layout` method returns a Plotly Layout object configured according to the provided layout dictionary.

The `figure` property generates and returns a complete Plotly Figure object, combining the data traces and layout settings. It ensures that the figure uses the default theme from Plotly version 3.x by setting the template to None.

Note: The BaseGraph class is intended to be extended or used directly for creating simple plots. For more complex layouts involving multiple subplots, classes like SubplotsGraph can utilize `BaseGraph` to create individual graph objects and integrate them into a single figure.

Output Example: A possible appearance of the code's return value could be a Plotly Figure object representing a line chart if `_graph_type` is 'Scatter'. This figure would display data from each column in the DataFrame as separate lines, with x-axis values taken from the DataFrame index and y-axis values from each column. Each line would have a label corresponding to its column name or a user-specified name from `name_dict`. The layout of the chart could include titles, axis labels, and other settings defined in the `layout` dictionary.
### FunctionDef __init__(self, df, layout, graph_kwargs, name_dict)
**__init__**: Initializes a BaseGraph object by setting up essential attributes such as the DataFrame, layout, graph parameters, and name dictionary. It also prepares the data for visualization by calling internal initialization methods.

parameters:
· df: A pandas DataFrame containing the data to be visualized. If not provided, it defaults to None.
· layout: A dictionary specifying go.Layout parameters for the graph. Defaults to an empty dictionary if not provided.
· graph_kwargs: A dictionary of additional keyword arguments used as graph parameters (e.g., settings for a bar chart). Defaults to an empty dictionary if not provided.
· name_dict: A dictionary mapping column names in the DataFrame to user-friendly or context-specific names. If not provided, it defaults to None.
· kwargs: Additional keyword arguments that can be used to customize the initialization process further.

Code Description: The __init__ method starts by assigning the provided DataFrame `df` to an internal attribute `_df`. It then initializes `_layout`, `_graph_kwargs`, and `_name_dict` with the provided dictionaries or defaults to empty dictionaries if none are given. The `data` attribute is initialized as None, which will later hold graph objects generated from the data.

The method proceeds by calling `_init_parameters(**kwargs)`, which sets up internal parameters such as the graph type and column name mappings based on the provided arguments. Following this, it calls `_init_data()`, which checks if the DataFrame is empty and raises a ValueError if it is. If the DataFrame contains valid data, `_init_data()` generates graph objects using the data in `_df` and assigns them to the `data` attribute.

Note: This function is typically called when creating an instance of BaseGraph. It ensures that all necessary attributes are initialized correctly before any visualization operations can be performed. The use of additional keyword arguments (`kwargs`) allows for further customization during object initialization, making the class flexible and adaptable to various visualization needs.
***
### FunctionDef _init_data(self)
**_init_data**: This function initializes the `data` attribute of an instance by generating graph objects based on the data contained within the instance's DataFrame (`self._df`). It ensures that the DataFrame is not empty before proceeding with data generation.

**parameters**:
· None: This function does not accept any direct parameters. Instead, it relies on the internal state of the instance (`self._df`, `self._graph_type`, `self._name_dict`, `self._graph_kwargs`) to generate its output.

**Code Description**: The function `_init_data` first checks if the DataFrame `self._df` is empty. If it is, a `ValueError` is raised with the message "df is empty." This step ensures that the subsequent operations are performed on valid data. After confirming that the DataFrame contains data, the function calls `_get_data`. The `_get_data` method generates a list of graph objects based on the data in `self._df`, using configuration details stored in other attributes (`self._graph_type`, `self._name_dict`, `self._graph_kwargs`). These graph objects are then assigned to the instance's `data` attribute, making them available for further use in visualization or reporting.

**Note**: This function is typically called during the initialization of a `BaseGraph` object. It ensures that the necessary data structures are prepared before any visualizations can be created or manipulated.

**Output Example**: If an instance of `BaseGraph` is initialized with a DataFrame containing two columns 'A' and 'B', mapped to graph names 'Series A' and 'Series B' in `self._name_dict`, and a graph type of 'Scatter', the `_init_data` function will populate the `data` attribute with a list of two Scatter graph objects. The first object plots the data from column 'A' against its index, labeled as 'Series A', while the second object plots the data from column 'B' against its index, labeled as 'Series B'. These graph objects can then be used to construct visualizations or reports.
***
### FunctionDef _init_parameters(self)
**_init_parameters**: This function initializes specific parameters related to the graphical representation of data within a BaseGraph object. It sets up essential attributes such as graph type and column name mappings based on provided arguments.

parameters:
· kwargs: A dictionary containing additional keyword arguments that can be used to customize the initialization process further.

Code Description: The function begins by setting the `_graph_type` attribute, which is derived from the lowercase version of the `_name` attribute (which presumably holds a string representing the type of graph), capitalized. This ensures consistency in how different types of graphs are referred to throughout the object's lifecycle.

Next, it checks if the `_name_dict` attribute is `None`. If so, it initializes `_name_dict` as a dictionary where each key-value pair corresponds to a column name from the DataFrame (`_df`) and its own name. This mapping is useful for translating internal data representations into more user-friendly or context-specific names when rendering the graph.

Note: Usage points include calling this function during the initialization of a BaseGraph object, typically after setting up basic attributes like `_df`, `_layout`, `_graph_kwargs`, and `_name_dict`. The `kwargs` parameter allows for additional customization options that can be processed within this method or passed further down to other methods handling graph rendering.
***
### FunctionDef get_instance_with_graph_parameters(graph_type)
**get_instance_with_graph_parameters**: This function dynamically retrieves a graph object instance based on the specified graph type and additional parameters. It attempts to import the graph class from `plotly.graph_objs` first; if it fails due to an AttributeError, it tries importing from `qlib.contrib.report.graph`.

parameters:
· graph_type: A string specifying the type of graph to be created (e.g., 'Scatter', 'Bar'). If not provided, the function will raise an error.
· **kwargs: Additional keyword arguments that are passed to the constructor of the graph class. These typically include data points and styling options.

Code Description: The function starts by attempting to import the `plotly.graph_objs` module and retrieve the specified graph class using the `getattr` function based on the `graph_type`. If an AttributeError is raised, indicating that the specified graph type does not exist in `plotly.graph_objs`, it then tries importing from `qlib.contrib.report.graph`. Once the correct graph class is obtained, it creates an instance of this class by passing all additional keyword arguments (`**kwargs`) to its constructor. The function finally returns this newly created graph object.

Note: This function is used within other methods such as `_get_data` and `_init_figure` in the `BaseGraph` and `SubplotsGraph` classes respectively, where it helps in generating specific types of graphs with given data and styling options.

Output Example: If called with `graph_type='Scatter'`, x=[1, 2, 3], y=[4, 5, 6], name='Sample Data', the function would return a Scatter graph object configured to plot the points (1,4), (2,5), and (3,6) labeled as 'Sample Data'. This object can then be added to a figure for display.
***
### FunctionDef _get_layout(self)
**_get_layout**: This function returns a Plotly graph layout object configured based on the internal `_layout` attribute of the `BaseGraph` class.
parameters:
· None: The function does not accept any parameters.

Code Description: The `_get_layout` method is designed to encapsulate the creation and return of a Plotly Layout object. It leverages the `go.Layout` constructor from the Plotly graph objects module, passing in keyword arguments unpacked from the `_layout` attribute of the class instance. This attribute presumably contains a dictionary or similar mapping that defines various layout properties such as title, axis labels, margins, and more.

Note: The function is intended to be used internally within the `BaseGraph` class, particularly by methods like `figure`, which require a properly configured layout for rendering graphs. By centralizing layout creation in `_get_layout`, the code promotes reusability and maintainability.

Output Example: A possible return value of this function could be an instance of `go.Layout` with properties set according to the `_layout` attribute, such as:
```
Layout({
    'title': {'text': 'Sample Graph'},
    'xaxis': {'title': 'X Axis Label'},
    'yaxis': {'title': 'Y Axis Label'},
    'margin': {'l': 40, 'r': 10, 't': 80, 'b': 20}
})
```
***
### FunctionDef _get_data(self)
**_get_data**: This function generates a list of graph objects based on the data contained within an instance's DataFrame (`self._df`) and configuration details stored in other attributes (`self._graph_type`, `self._name_dict`, `self._graph_kwargs`). It iterates over each column specified in `self._name_dict` to create a corresponding graph object for visualization.

**parameters**:
· None: This function does not accept any direct parameters. Instead, it relies on the internal state of the instance (`self._df`, `self._graph_type`, `self._name_dict`, `self._graph_kwargs`) to generate its output.

**Code Description**: The function `_get_data` begins by initializing a list comprehension that iterates over items in `self._name_dict`. Each item in this dictionary represents a column name (`_col`) and an associated graph name (`_name`). For each iteration, the function calls `self.get_instance_with_graph_parameters`, passing it the graph type (`self._graph_type`), x-values (the index of `self._df`), y-values (the values from the specified column `_col` in `self._df`), and a name for the graph (`_name`). Additional keyword arguments stored in `self._graph_kwargs` are also passed to customize the graph. The result is a list of graph objects, each configured according to the provided parameters.

**Note**: This function is typically called within `_init_data` to initialize the `data` attribute of an instance with the generated graph objects. These graph objects can then be used to construct visualizations or reports.

**Output Example**: If `self._df` contains data points for two columns 'A' and 'B', and `self._name_dict` maps these columns to names 'Series A' and 'Series B', respectively, with a graph type of 'Scatter', the function would return a list containing two Scatter graph objects. The first object would plot the data from column 'A' against its index, labeled as 'Series A', while the second object would plot the data from column 'B' against its index, labeled as 'Series B'. These objects can be added to a figure for display or further manipulation in visualization tools.
***
### FunctionDef figure(self)
**figure**: This function generates a Plotly Figure object using the data stored within the class instance and a layout configuration obtained from the `_get_layout` method.

parameters:
· None: The function does not accept any parameters.

Code Description: The `figure` method is responsible for creating and returning a Plotly Figure object. It initializes this figure with two main components: `data`, which represents the data to be plotted, and `layout`, which defines how the plot should look (e.g., titles, axis labels, margins). The `data` attribute is directly used from the class instance, while the layout is generated by calling the `_get_layout` method. After creating the figure, it explicitly sets the template to `None` to ensure that the default theme from Plotly version 3.x is used.

Note: This function is typically called when a visual representation of data needs to be created and displayed or saved. It encapsulates the process of combining data with layout settings into a cohesive Plotly Figure object, making it easier for users to generate plots without manually configuring each aspect.

Output Example: A possible return value of this function could be an instance of `go.Figure` with data points and a layout configuration similar to:
```
Figure({
    'data': [Scatter({'x': [1, 2, 3], 'y': [4, 5, 6]})],
    'layout': Layout({
        'title': {'text': 'Sample Graph'},
        'xaxis': {'title': 'X Axis Label'},
        'yaxis': {'title': 'Y Axis Label'},
        'margin': {'l': 40, 'r': 10, 't': 80, 'b': 20}
    })
})
```
***
## ClassDef SubplotsGraph
**SubplotsGraph**: A class designed to create subplots from a pandas DataFrame using Plotly's subplot functionality. It simplifies the process of generating multiple plots within a single figure, allowing for customization of each subplot's appearance and layout.

attributes:
· df: A pandas DataFrame containing the data to be plotted.
· kind_map: A dictionary specifying the type of plot (e.g., 'Scatter', 'Bar') and any additional keyword arguments for each subplot. If not provided, defaults to a scatter plot with no additional arguments.
· layout: A dictionary of parameters that will be applied to the overall figure's layout.
· sub_graph_layout: Similar to `layout`, but specifically for individual subplots.
· sub_graph_data: A list of tuples where each tuple contains a column name from the DataFrame and a dictionary specifying the subplot's row, column, name, kind, and graph-specific keyword arguments. If not provided, it will be automatically generated based on the DataFrame columns.
· subplots_kwargs: A dictionary containing parameters for Plotly's `make_subplots` function, such as shared axes, vertical spacing, and grid specifications. Defaults are set if not provided.

Code Description: The SubplotsGraph class initializes with various parameters that define how the subplots should be created and styled. If certain parameters like `sub_graph_data` or `subplots_kwargs` are not provided, default values are generated based on the DataFrame's structure. The `_init_sub_graph_data` method creates a list of subplot configurations if none is provided, positioning each subplot in a grid layout determined by the number of columns and rows. The `_init_subplots_kwargs` method sets up default parameters for creating subplots if no specific settings are given. The `_init_figure` method constructs the final figure by adding traces to the subplot grid based on the configurations specified.

Note: This class is particularly useful for generating complex reports or visualizations that require multiple plots, such as time series data analysis or performance metrics comparison. It leverages Plotly's powerful plotting capabilities while providing a user-friendly interface for defining subplot arrangements and styles.

Output Example: The output of this class is a Plotly figure object containing the specified subplots. For instance, if `report_figure` function from the provided code snippet uses SubplotsGraph to create a report with cumulative returns, maximum drawdowns, turnover rates, etc., the resulting figure might display multiple line plots stacked vertically, each representing different financial metrics over time. The figure would include shared x-axes for easier comparison and custom styling as defined in `_layout_style` and `_subplot_layout`.
### FunctionDef __init__(self, df, kind_map, layout, sub_graph_layout, sub_graph_data, subplots_kwargs)
Doc is waiting to be generated...
***
### FunctionDef _init_sub_graph_data(self)
**_init_sub_graph_data**: This function initializes the sub-graph data and subplot titles for a SubplotsGraph object when no specific sub-graph data is provided during initialization.

parameters:
· No explicit parameters: The method uses internal attributes of the SubplotsGraph class such as `self._df`, `self.__cols`, `self._kind_map`, and `self._subplots_kwargs` to perform its operations.

Code Description: Detailed analysis and description.
The function `_init_sub_graph_data` is designed to populate two key attributes of a SubplotsGraph object: `_sub_graph_data` and `_subplot_titles`. These attributes are essential for defining the layout and content of each subplot within a larger figure. The method iterates over each column in the DataFrame `self._df`, which serves as the data source for generating subplots.

For each column, it calculates the row (`row`) and column (`col`) positions where the subplot will be placed based on the total number of columns specified by `self.__cols`. This is achieved using mathematical operations to determine how many rows are needed and which specific cell in a grid layout (defined by rows and columns) should host each subplot.

The function also formats the column name for display purposes, replacing underscores with spaces. It then constructs a tuple containing the original column name and a dictionary of parameters that define the subplot's characteristics such as its position (`row` and `col`), display name (`name`), type (`kind`), and additional styling options (`graph_kwargs`). This tuple is appended to `_sub_graph_data`.

Simultaneously, the formatted column name (now used as the subplot title) is added to `_subplot_titles`. These titles are later utilized in labeling each subplot within the figure.

Note: Usage points.
This function is automatically invoked during the initialization of a SubplotsGraph object if no `sub_graph_data` parameter is provided. It ensures that subplots are generated for all columns in the DataFrame with default settings unless overridden by specific parameters passed to the constructor.

Output Example: Mock up a possible appearance of the code's return value.
Assuming a DataFrame with three columns named 'data_1', 'data_2', and 'data_3' and `self.__cols` set to 2, `_init_sub_graph_data` would produce:

- _sub_graph_data:
[
    ('data_1', {'row': 1, 'col': 1, 'name': 'data 1', 'kind': 'Scatter', 'graph_kwargs': {}}),
    ('data_2', {'row': 1, 'col': 2, 'name': 'data 2', 'kind': 'Scatter', 'graph_kwargs': {}}),
    ('data_3', {'row': 2, 'col': 1, 'name': 'data 3', 'kind': 'Scatter', 'graph_kwargs': {}})
]

- _subplot_titles:
['data 1', 'data 2', 'data 3']
***
### FunctionDef _init_subplots_kwargs(self)
**_init_subplots_kwargs**: This function initializes a dictionary containing default keyword arguments for creating subplots using Plotly's `make_subplots` method. It sets up parameters such as the number of rows and columns, whether axes should be shared, spacing between plots, and titles for each subplot.

parameters:
· None: This function does not accept any direct parameters. However, it relies on the instance variable `_df`, which is a DataFrame containing data to be plotted.

Code Description: The function begins by setting default values for the number of columns (`_cols`) and rows (`_rows`). The number of rows is calculated based on the length of the DataFrame's columns divided by two, rounded up. A dictionary named `_subplots_kwargs` is then initialized to store these parameters along with other settings like `shared_xaxes`, `shared_yaxes`, `vertical_spacing`, and `print_grid`. The `subplot_titles` are set to match the column names from the DataFrame.

Note: This function is typically called during the initialization of a `SubplotsGraph` object if no custom `subplots_kwargs` are provided. It ensures that there are default settings for creating subplots, which can be overridden by user-specified parameters.

Output Example: A possible appearance of the code's return value (stored in `_subplots_kwargs`) could look like this:
```
{
    "rows": 3,
    "cols": 2,
    "shared_xaxes": False,
    "shared_yaxes": False,
    "vertical_spacing": 0.1,
    "print_grid": False,
    "subplot_titles": ['Column1', 'Column2', 'Column3', 'Column4', 'Column5', 'Column6']
}
```
This example assumes a DataFrame with six columns, resulting in three rows and two columns of subplots.
***
### FunctionDef _init_figure(self)
**_init_figure**: This function initializes a Plotly figure object with multiple subplots based on specified parameters. It creates individual graph objects for each subplot, adds them to the figure at designated positions, and applies layout settings.

parameters:
· None: The function does not accept any direct parameters but relies on instance attributes set during initialization of the SubplotsGraph class.

Code Description: The function begins by creating a Plotly figure object using `make_subplots` with keyword arguments stored in `_subplots_kwargs`. It then iterates over `_sub_graph_data`, which contains tuples specifying column names and their corresponding graph parameters. For each tuple, it checks if the column name is an instance of `go.Figure`. If so, it uses this figure object directly; otherwise, it assumes the column name is a string and creates a new graph object using `BaseGraph.get_instance_with_graph_parameters`, passing in the kind of plot, x and y data, name, and any additional graph-specific keyword arguments. The row and column positions for each subplot are determined from `_sub_graph_data` and used to add the trace to the figure at the correct location.

After all traces have been added, the function checks if there are specific layout settings for individual subplots in `_sub_graph_layout`. If so, it updates these settings on the figure. Finally, it sets the template of the figure's layout to `None` to use Plotly version 3.x default theme and applies any additional overall layout settings from `_layout`.

Note: This function is typically called during the initialization of a SubplotsGraph object to set up the visual representation of data across multiple subplots.

Output Example: If `_sub_graph_data` contains parameters for two scatter plots, one in row 1 column 1 and another in row 2 column 1, and `_layout` specifies a title "Data Visualization", the function would return a Plotly Figure object with two scatter plots arranged vertically. The first subplot displays data from the first specified column against the DataFrame index, and the second subplot does the same for the second column. Both subplots are labeled according to their respective names provided in `_sub_graph_data`, and the entire figure has a title "Data Visualization".
***
### FunctionDef figure(self)
**figure**: This function returns the figure object associated with an instance of SubplotsGraph. The figure object encapsulates a multi-subplot layout, each displaying different financial metrics calculated from input data.

parameters:
· No parameters: The `figure` method does not accept any parameters directly. It operates on the internal state of the SubplotsGraph instance it belongs to.

Code Description: Detailed analysis and description.
The `figure` function is a simple accessor method designed to provide external access to the `_figure` attribute of the SubplotsGraph class. This attribute holds a Plotly figure object, which represents a complex layout with multiple subplots. Each subplot corresponds to different financial metrics such as cumulative returns, maximum drawdown, and turnover rates.

The figure object is constructed within the `report_figure` function in the same module (`qlib_report_figure.py`). During this construction process, various parameters are defined and passed to an instance of SubplotsGraph:
- `df`: A DataFrame containing financial data.
- `layout`: A dictionary specifying the overall layout style of the figure, including dimensions, title, and shapes for highlighting specific regions (e.g., maximum drawdown periods).
- `sub_graph_data`: A list of tuples mapping column names from the DataFrame to subplot positions and additional graphing parameters.
- `subplots_kwargs`: Arguments controlling the arrangement and spacing of subplots within the figure.
- `kind_map`: Default kind of plot for each subplot, along with any specific keyword arguments required by Plotly.
- `sub_graph_layout`: Additional layout settings for individual subplots.

After initializing the SubplotsGraph instance with these parameters, the `figure` method is called to retrieve and return the constructed figure object. This object can then be used for further manipulation or rendering in a web application or Jupyter notebook using Plotly's visualization capabilities.

Note: Usage points.
The primary use of this function is to obtain the fully configured figure object after all subplots have been set up according to the specified parameters. Developers can call this method on an instance of SubplotsGraph to access the underlying figure for additional customization, saving, or displaying purposes.

Output Example: Mock up a possible appearance of the code's return value.
The output of the `figure` function is a Plotly Figure object that visually represents financial metrics across multiple subplots. For example, one subplot might show cumulative returns over time with lines and markers, while another could display turnover rates as a bar chart or line graph. The figure includes annotations for maximum drawdown periods, ensuring these critical points are highlighted to the viewer. Overall, the figure provides a comprehensive visual summary of financial performance metrics, aiding in quick analysis and decision-making processes.
***
## FunctionDef _calculate_maximum(df, is_ex)
**_calculate_maximum**: This function calculates the start and end dates of the maximum drawdown period from a given DataFrame. It can compute this for either the regular returns or the ex-returns based on the `is_ex` flag.

parameters:
· df: A pandas DataFrame containing financial data, including cumulative return columns.
· is_ex: A boolean flag indicating whether to calculate the maximum drawdown for ex-returns (True) or regular returns (False).

Code Description: The function first checks the value of `is_ex`. If it's True, it identifies the end date as the index where the 'cum_ex_return_wo_cost_mdd' column reaches its minimum value. It then finds the start date by locating the maximum value in the 'cum_ex_return_wo_cost' column up to and including this end date. If `is_ex` is False, it performs a similar operation but uses the 'return_wo_mdd' and 'cum_return_wo_cost' columns instead. The function returns a tuple containing the calculated start and end dates.

Note: This function assumes that the DataFrame `df` contains specific column names ('cum_ex_return_wo_cost_mdd', 'cum_ex_return_wo_cost', 'return_wo_mdd', 'cum_return_wo_cost') which represent different types of cumulative and non-cumulative returns, with or without costs. The function is designed to be used in financial data analysis, particularly for identifying periods of maximum drawdown in investment performance.

Output Example: If the DataFrame `df` contains daily return data and `is_ex` is False, the function might return ('2023-01-15', '2023-02-10'), indicating that the period from January 15 to February 10 experienced the maximum drawdown based on the non-ex returns without costs.
## FunctionDef _calculate_mdd(series)
**_calculate_mdd**: This function calculates the Maximum Drawdown (MDD) of a given time series data. MDD is a measure used in finance to quantify the largest loss from a peak to a trough during a specific period for an investment or fund.

**parameters**:
· series: A pandas Series object representing cumulative returns over time, typically derived from financial data such as stock prices or investment returns.

**Code Description**: The function computes the MDD by subtracting each value in the input series from its cumulative maximum. This operation highlights the point at which the series falls below its peak, indicating a drawdown. By calculating the difference between the current value and the highest value reached up to that point, the function effectively measures how much the series has declined relative to its peak.

**Note**: The function is designed to be used with cumulative return data, where each element in the series represents the total return from the start of the period up to that point. It is commonly used in financial analysis to assess risk and performance metrics of investment strategies or portfolios.

**Output Example**: If the input series is [100, 120, 150, 140, 130], representing cumulative returns over five periods, the function would return a series [0, 0, 0, -10, -20]. This output indicates that there was no drawdown in the first three periods (returns were always increasing), but then the series fell by 10% from its peak of 150 to 140, and further declined by an additional 10% to reach 130.
## FunctionDef _calculate_report_data(raw_df)
**_calculate_report_data**: This function processes raw financial data to generate a comprehensive report DataFrame containing various performance metrics such as cumulative returns, maximum drawdowns, excess returns, and turnover.

parameters:
· raw_df: A pandas DataFrame object that contains the raw financial data, including columns for benchmark returns ("bench"), investment returns ("return"), costs associated with transactions ("cost"), and turnover rates ("turnover").

Code Description: The function begins by creating a deep copy of the input DataFrame to avoid modifying the original data. It then formats the index of this DataFrame to ensure it consists of date strings in the "YYYY-MM-DD" format, which is crucial for time series analysis.

A new DataFrame named `report_df` is initialized to store the calculated metrics. The function computes cumulative sums for benchmark returns ("cum_bench"), investment returns without costs ("cum_return_wo_cost"), and investment returns with costs deducted ("cum_return_w_cost"). These cumulative sums are essential for assessing overall performance over time.

Next, the function calculates the maximum drawdown (MDD) for both the cumulative returns without costs ("return_wo_mdd") and the cumulative returns after accounting for costs ("return_w_cost_mdd"). This is achieved by calling another internal function `_calculate_mdd`, which measures the largest decline from a peak to a trough in the cumulative return series.

The function also computes excess returns, which are the differences between investment returns and benchmark returns. It calculates these excess returns both without costs ("cum_ex_return_wo_cost") and with costs deducted ("cum_ex_return_w_cost"). Similar to the previous calculations, MDDs for these excess returns are computed as well.

Turnover rates from the original DataFrame are directly copied into `report_df`. The resulting DataFrame is then sorted by index in ascending order to ensure chronological ordering of data points. Finally, the function restores the original index names before returning the fully populated `report_df`.

Note: This function is designed to be used with financial time series data where each row corresponds to a specific date and includes relevant performance metrics. It provides a comprehensive set of calculations that are commonly used in investment analysis to evaluate strategy performance.

Output Example: If the input DataFrame contains daily returns for an investment portfolio along with benchmark returns, costs, and turnover rates, the output would be a DataFrame with columns such as "cum_bench", "cum_return_wo_cost", "cum_return_w_cost", "return_wo_mdd", "return_w_cost_mdd", "cum_ex_return_wo_cost", "cum_ex_return_w_cost", "turnover". Each column would contain calculated values for each date in the index, allowing for detailed performance analysis. For instance, a row might look like this:

| Date       | cum_bench | cum_return_wo_cost | cum_return_w_cost | return_wo_mdd | return_w_cost_mdd | cum_ex_return_wo_cost | cum_ex_return_w_cost | turnover |
|------------|-----------|--------------------|-------------------|---------------|-------------------|-----------------------|----------------------|----------|
| 2023-01-01 | 0.05      | 0.07               | 0.06              | -0.02         | -0.03             | 0.02                  | 0.01                 | 0.10     |

This example illustrates how the function transforms raw financial data into a structured format suitable for further analysis and visualization.
## FunctionDef report_figure(df)
Doc is waiting to be generated...
