## FunctionDef fit(X_train, y_train, X_valid, y_valid)
**fit**: Define and train a Linear Regression model using training data, validate it with validation data, and return the trained model.
parameters:
· X_train: A pandas DataFrame containing the features for the training dataset.
· y_train: A pandas Series containing the target variable for the training dataset.
· X_valid: A pandas DataFrame containing the features for the validation dataset.
· y_valid: A pandas Series containing the target variable for the validation dataset.

Code Description: The function initializes a Linear Regression model using scikit-learn's `LinearRegression` class. It then fits this model to the provided training data (X_train and y_train). After fitting, the function validates the model by making predictions on the validation set (X_valid) and calculating the Mean Squared Error (MSE) between these predictions and the actual values in y_valid. The MSE is printed out with a precision of four decimal places to provide an immediate feedback on the model's performance on unseen data. Finally, the function returns the trained Linear Regression model.

Note: This function assumes that the input datasets are preprocessed and ready for training, meaning they should not contain missing values or categorical variables that need encoding unless these have been handled beforehand. The function does not include any feature selection steps within its pipeline; it is expected that any necessary feature selection has already been applied to X_train and X_valid.

Output Example: Assuming the validation predictions are close to the actual values, a possible output could be:
Validation Mean Squared Error: 0.1234
The function would then return the trained `LinearRegression` model object which can be used for further analysis or making predictions on new data.
## FunctionDef predict(model, X)
**predict**: This function takes a trained machine learning model and input data to make predictions while ensuring consistency in feature selection.

parameters:
· model: A pre-trained machine learning model that is capable of making predictions based on the provided input features.
· X: Input data, typically a 2D array or DataFrame where each row represents an instance and each column represents a feature used for prediction.

Code Description: The function predict is designed to utilize a previously trained model to generate predictions. It accepts two main arguments - the model itself and the dataset on which predictions are to be made (X). Inside the function, the predict method of the model object is called with X as its argument, which returns the predicted values. These predicted values are then reshaped into a 2D array with one column using the reshape(-1, 1) method before being returned. This reshaping step ensures that the output format is consistent and can be easily used in further processing or analysis.

Note: It's important to ensure that the input data X has the same features as those used during the training of the model to maintain consistency in feature selection. The order and type of features should match exactly with what the model expects.

Output Example: If the function is called with a trained linear regression model and an input dataset containing 5 instances, the output might look like this:

[[12.34]
 [15.67]
 [9.87]
 [10.11]
 [13.45]]

Each sub-array represents the predicted fare for one instance in the input data X.
