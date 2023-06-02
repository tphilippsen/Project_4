![My Image](Data%20Images/Walmart_logo%202.png?raw=true)

# Walmart-Sales-Forecast
Walmart department sales by week from Feb 2010 - Oct 2012

Data Source: https://www.kaggle.com/datasets/ujjwalchowdhury/walmartcleaned/discussion?resource=download&group=bookmarked
 | Column Name   | Type    | Description              |
  | ------------- | ------- | ------------------------ |
  | Store | Int     | Store ID number 
  | Date      | Date  | Year-Month-Day            
  | IsHoliday      | Boolean  | Indicates if instance was a holiday week
  | Dept     | Int | Department ID number
  | Weekly_Sales | Float  | Sum of sales over given week in dollars
  | Temperature        | Float  | Temperature in degree Celsius
  | Fuel_Price       | Float  | Cost of fuel in the region in dollars
  | MarkDown(1-5)      | Float  | Markdown event precede the four prominent holidays
  | CPI         | Float  | Prevailing consumer price index
  | Unemployment      | Int     | Prevailing unemployment rate
  | Type | Int  | Store labeled 1-3 based on size of store
  | Size | Int | Size of the store in Sq Ft
  
- Original data set contains 16 columns and 400,000+ rows comprising of data from 45 different stores from a region. 

- Our original data set was uploaded as a csv.

- Used 'dt.isocalendar' function to parse the number of weeks in the year for each instance and created a new column labeled 'week_of_year'.

- Created new columns for our 4 main spending holidays marked by IsHoliday: Superbowl, Labor Day, Thanksgiving, and Christmas. We used the '.loc' function to filter the data based on 'week_of_year' to assign each instance a 0 or 1 value for all 4 holidays. Our data set reflects the holiday and its corresponding week instead of a generic holiday value. 

- Used the 'dt.year' function to extract the year from the 'Date' values for usability purposes. 

- Aggregated data by Date and 'week_of_year' to forecast sales for the region. Weekly_Sales was totaled for the aggregation and values averaged for the week for all other non-holiday feature columns. Dept, Type and Store were dropped from the aggregation.

## Polynomial Regression

- For our model based on weekly sales per store, we looked at all 45 stores and grouped by the Store, Date and week_of_year.

- We began by creating a scatter plot comparing Size of the store to Weekly sales.

- Based on the initial scatter plot, our data did not appear to be a good fit for a linear regression model, so we built a polynomial regression model.

- We then used the SKlearn 'PolynomialFeatures' function to build the model and find our predicted y values. We found the best model had 3 degrees.

- Our polynomial regression had an r^2 value of 0.41. This information tells us that we were able to fit only about 40% of our data and feature sets were not included. 

<p align="center">
  <img width=400px height=240px src="https://github.com/MEJillFarley/Walmart-Sales-Forecast/blob/4c48dbc3119464636761869eee79eae556a34a7d/Data%20Images/Screenshot%202023-04-13%20at%207.29.20%20PM.png">
</p>

- We concluded that a polynomial regression model was not sufficient for our goal to predict Walmart sales and store level forecast might not apply to all stores. We decided to build a random forest model to see if we could more accurately predict sales for the region using cumulative feature sets from all stores.

------------------------
# Machine Learning

## RandomForest Regression Model

- Our team chose to build a RandomForest Regression model due to it's ability to produce good predictions with averages from large data sets.

- Goal was to build a model to predict Weekly sales with cumulative feature sets for all stores.

#### Preprocessing
- Split data into train and test sets using  Sklearn train_test_split function.
- Used StandardScaler module to scale our data.

#### Build and Predict
- Initialized RandomForestRegressor to get a base model
- Fit the training dataset to train the model
- Predict using test data
- Review and tune to achieve cloase to 90% fitting (r^2 value of 0.8969 and RMSE about 1.76 million USD). A significant improvement in prediction from the model.

#### Optimization
- We started off with an initial r^2 score of .65 and about 50 million in variation.
- Study indicated missing markdowns was the major cause and we were missing values from original dataset for almost all of 2010 and 2011. We decided to take the average per week and use it for all years.
- This improved fitting to about 0.74
- We then included close to holiday indicators for Easter, Thanksgiving and Christmas to bring it close to 80% fitting
- Two different lags were then introduced as features. Previous week and then last 4 weeks.
- We increased the number of trees and finally set to 1000 to come up with a good 0.89

#### Final Prediction for all years using the model
<p align="center">
  <img width=400px height=240px src="https://github.com/MEJillFarley/Walmart-Sales-Forecast/blob/main/Data%20Images/predictions_all.png">
</p>

#### Limitations
- Location of the stores is not known and we have built the model with the assumption of all stores being in the same region.
- Markdown data was missing for 2010 and most of 2011. We used the average of data that was present which left 0 values for week 44.
- Markdowns are not the same each year and missing value for week 44 around pre-Thanksgiving is a major limitation.
- A good history is required for time-series prediction but our dataset roughly covers about 2 years and 9 months for history.
