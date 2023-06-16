# Framing the Problem
I am choosing to solve the problem of predicting the duration of power outages. As such, this is a regression problem, involving the prediction of a quantitative continuous variable. In the dataset, this would be predicting the 'OUTAGE.DURATION' column, as that column is a direct representation of what the prediction task would try to do. I used RMSE (root mean squared error) in order to evaluate my model. I used this metric for one major reason, which is interpretability. RMSE provides me the ability to match units and easily understand the error rather than another metric such as MSE that has, in this case, a unit of hours^2. My error in this case would represent that average hours off I am in each prediction, giving me a good understanding of my model's performance intuitively.<br>
During preprocessing, the number on column I wanted to remove from the dataset was 'OUTAGE.RESTORATION.TIME' and 'OUTAGE.RESTORATION.DATE', as we would obviously not know when power would be restored if that is the prediction problem. This is the only column I decided to not include in the dataset for the sake of creating a proper realistic prediction problem to solve. Any other removal of columns as simply to streamline my data in a manner I felt would be useful to solve the prediction task.<br>

# Baseline Model
The model I created as my baseline was a DecisionTreeRegressor model with a max_depth of 2. Below is the data regarding the use of my features.<br>

NERC.REGION: Categorical (Nominal) -> OrdinalEncoder()<br>
ANOMALY.LEVEL: Quantitative (Continuous) -> StandardScaler()<br><br>
1 Nominal Feature, 1 Continuous Feature<br><br>

This baseline model achieved an RMSE of 73.13748322195806 hours. The interpretation of this is that the average error across the predictions was about 73 hours. This is an average error of just over 3 days, which isn't very good.

# Final Model
New features I added include the following: 'OUTAGE.START.TIME' and 'U.S._STATE'. I believe these were both good features to add to our prediction task. The biggest reason for 'OUTAGE.START.TIME' was that I believe there may be time dependent patterns, which encompasses seasonality. I also used 'U.S._STATE' as the other feature because of potential specific geographic patterns that could be present. It creates a more specific picture than 'CLIMATE.REGION' and could extract nuanced patterns in the data.<br>

I chose to divert from the DecisionTreeRegressor and chose ElasticNet Regression algorithm instead due to its flexibility to add two regularization techniques to combat the multicollinearity problem (the techniques that are used in Lasso and Ridge regression). As such, I decided to tune the hyperparameters 'alpha' and 'l1_ratio'. This is because these two are the direct parameters of the regularization penalty that is added to the regression function, more easily influencing the outcome of the model. In order to find the two most optimal parameters, I chose to loop through all combinations of alpha ([0.01, 0.1, 1, ..., 10_000]) and l1_ratio ([0.01, 0.02, 0.03, ..., 0.99, 1]) and chose the combo that resulted in the lowest RMSE. This ended up being an alpha of 1 and an l1_ratio of 0.97. This resulted in my RMSE decreasing to 71.39593079478831 hours. 

# Fairness Analysis
The two groups I decided to evaluate my model on were outages that started in the first half of the year (Jan - June) and outages that started in the second half of the year (Jul - Dec).<br>

Evaluation Metric: Root Mean Squared Error (RMSE)<br>
Null Hypothesis: The error between the first half of the year and the second half of the year is the same, indicating our model has fair performance in both halves of the calendar.<br>
Alternative Hypothesis: The error between the first half of the year is greater than the second half of the year, indicating our model is biased in its prediction ability in the halves of the calendar year.<br>
Test Statistic: (First Half RMSE) - (Second Half RMSE)<br>
Significance Level: 0.01<br>
P Value: 0.2056<br>
Conclusion: We failed to reject the null hypothesis. We tentitively cannot divert from the claim that the model is unfair in being more error prone in the first half of the calendar year compared to the second half.