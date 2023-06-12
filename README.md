# Framing the Problem
I am choosing to solve the problem of predicting the length of power outage times. As such, this is a regression problem, involving the prediction of a continuous variable. In the dataset, this would be predicting the 'OUTAGE.DURATION' column. I used RMSE (root mean squared error) in order to evaluate my model. I used this metric for multiple reasons. The first reason is that RMSE provides me the ability to match units and easily understand the error metric rather than another metric such as MSE that has, in this case, a unit of seconds^2. Also, RMSE will penalize larger errors more, allowing for more rigorous evaluation of my model against outlier data.<br>
During preprocessing, the number on column I wanted to remove from the dataset was 'OUTAGE.RESTORATION.TIME', as we would obviously not know when power would be restored if that is the prediction problem. This is the only column I decided to not include in the dataset for the sake of creating a proper realistic prediction problem to solve.<br>

# Baseline Model
The baseline model used was a linear regression model.<br>
For my transformations, I made extensive use of the OrdinalEncoder to give a unique number to each unique value in a column. I also used a FunctionTransformer that used a custom function to split the 'OUTAGE.START.TIME' column into individual columns of 'YEAR', 'MONTH', 'DAY', 'HOUR', and 'MIN'. I did not go to seconds as the data only went minute deep rather than seconds deep. 
Features:<br>
'U.S._STATE': Nominal, OrdinalEncoder()<br>  
'NERC.REGION': Nominal, OrdinalEncoder()<br> 
'CLIMATE.REGION': Nominal, OrdinalEncoder()<br>
'CAUSE.CATEGORY': Nominal, OrdinalEncoder()<br>
'ANOMALY.LEVEL': Ordinal, NA<br>
'OUTAGE.START.TIME': Quantitative, FunctionTransformer(extract_datetime_cols)<br><br>
Nominal: 4<br>
Ordinal: 1<br>
Quantitative: 1<br><br>
My baseline model's performance using an 80/20 train/test split on the test data was an RMSE of 108.91172500134348. I did not consider this model to be good. One problem was that the model would be prone to outputting negative numbers (negative hours) in scenarios of shorter outages, lacking interpretability. The other part of this model that makes it bad is the inability of this model to work with NaN values. As such, it forced me to not use the 'CUSTOMERS.AFFECTED' column due to the nearly 28 percent missingness, making imputating it extremely difficult.

# Final Model
The list of features I added are 'CAUSE.CATEGORY.DETAIL' and 'CUSTOMERS.AFFECTED'. For 'CAUSE.CATEGORY.DETAIL', this column contains detailed reasons for outages. This breaks down the 'CAUSE.CATEGORY' column into deeper sections that could find further relationships between more specific causes and time an outage took.



# Fairness Analysis