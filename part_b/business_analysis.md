## B1. Problem Formulation
# (a) What is the ML problem here?
Target variable (what we want to predict):

Items Sold in a store during a month.

Input features (what the model can use to make predictions):

Store details: store_size, location_type, footfall, competition_density

Promotion details: promotion_type, discount type, category targeted

Calendar details: month, weekend flag, festival flag

Past performance: previous month’s sales, trends

Type of ML problem:

This is a supervised regression problem because the output we want (items sold) is a number, not a category.

Regression is the right choice when the goal is to predict a continuous value.

# (b) Why use “items sold” instead of revenue?
Revenue can change a lot depending on:

product prices

discount levels

mix of expensive vs cheap items

Two stores may sell the same number of items but earn different revenue because of price differences.

Items sold is better because:

It directly shows how customers reacted to a promotion

It is not affected by price changes

It gives a clearer picture of demand

General principle:  
Choose a target variable that matches the real business goal.
If the goal is to understand promotion impact, sales volume is more reliable than revenue.

# (c) Why not one global model for all stores?
Stores in different areas (urban, semi‑urban, rural) behave differently.
A single model would mix all these patterns together and may miss important differences.

Better approach:

Build separate models for each store segment, or

Use one model but include strong store‑specific features (location, competition, footfall)

This respects the fact that promotions work differently in different environments.

## B2. Data and EDA Strategy

# (a) How to join the four tables

Tables:

Transactions

Store attributes

Promotion details

Calendar (weekend, festival)

How to join them:

Join transactions  with store attributes using store_id

Join transactions with promotions using promotion_id

Join transactions with calendar using transaction_date

Final dataset grain:  
One row = one store in one month with one promotion

Aggregations needed:

Sum items_sold per store per month

Add monthly footfall or competition metrics

Add calendar features (weekend count, festival flag)

Create lag features (previous month’s sales)

# (b) EDA to perform before modelling
Here are four beginner‑friendly analyses:

Sales vs Promotion Type (bar chart)

Shows which promotions generally work better

Helps understand promotion effectiveness

Monthly Sales Trend (line chart)

Shows seasonality (festivals, holidays, slow months)

Helps decide if month or season should be included as features

Sales by Store Size and Location (boxplots)

Shows how store characteristics affect sales

Helps decide if separate models are needed

Correlation Heatmap

Shows relationships between numeric features

Helps avoid using highly correlated features together

Each analysis helps shape the feature engineering and modelling strategy.

# (c) Handling imbalance (80% no‑promotion cases)
If most transactions have no promotion, the model may learn that “no promotion” is always the best choice.

This can cause:

Poor learning of rare promotions

Biased predictions

Incorrect recommendations

Fixes:

Balance the data (upsample rare promotions or downsample no‑promotion cases)

Add promotion frequency as a feature

Use techniques that focus on uplift (promotion impact)

## B3. Model Evaluation and Deployment

# (a) Train‑test split and metrics
Correct split:

Use older months for training

Use the most recent months for testing

This respects the time order

Why not random split?  
Random split mixes past and future data, which creates leakage and unrealistic results.

Metrics to use:

RMSE: penalizes large mistakes

MAE: average size of errors

MAPE (optional): percentage error, easy to explain to business teams

These metrics show how well the model predicts monthly items sold.

# (b) Explaining different recommendations for the same store
If the model recommends different promotions for Store 12 in December vs March:

Look at feature importance or SHAP values for each month

Identify which features influenced the prediction

Example explanation:

In December, festivals and high footfall may make Loyalty Points Bonus more effective

In March, competition or seasonal slowdown may make Flat Discount better

This helps the marketing team understand that the model adapts to changing conditions.

# (c) Deployment process (simple explanation)
1. Save the trained model

Use a file format like joblib

Store it safely so it can be reused every month

2. Monthly prediction process

Load the saved model

Prepare new monthly data (store attributes, promotions, calendar)

Apply the same preprocessing steps

Predict items sold for each promotion

Choose the promotion with the highest predicted value

3. Monitoring  
Watch for:

Increasing prediction errors

Changes in store behavior

New promotions or store types

Data drift (footfall or competition changes)

Retrain the model when performance drops or business conditions change.