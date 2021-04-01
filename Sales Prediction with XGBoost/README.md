![](img/banner.jpg)

# Sales Prediction with XGBoost

The goal of this project is to work with time-series data and use XGBoost to forecast sales for the number of products sold in every shop. A unique aspect of the dataset is that that the list of shops and products changes every month and there are new items in the testing dataset that are not in the training dataset.

## Data Processing

In this project, data processing is performed to remove outliers and missing targets value are imputed by calculating the mean median for the same item in the same shop sold during the same date block.

Although shop names are in Russian, there are still ways the data can be analyzed and processed. First, to determine duplicates in the shop names, I use the SequenceMatchter method to determine the similarity ratio for every pair of shop names. For pairs that had a similarity ratio > 0.8, I went through each pair manually to determine if they are the same store. Next, the city name is extracted from the shop name and cleaned.

Product type and subtypes are extracted and encoded with LabelEncoder.

## Time Series Analysis and Processing

There are 363 new items (with unique id) in the test set. Therefore, for most of the items in the test set the target value should be 0. The main idea is to calculate monthly sales and extend it with zero sales for each unique pair within the month. This way train data will be similar to test data.

I used the product function from itertools to create every possible combination of shop ID and item ID for each date block. There are 34 date blocks in total, so the matrix will have a length of 34. Inside each of the 34 date blocks, a row will be created for each unique shop ID and item ID. Since not all items are sold in every date block, the length (which represents each unique combo of shop ID and item ID) will be different.

I aggregated the train set by shop/item pairs to calculate target aggregates using the groupby() method. Then, flatten indexes with reset_index() to make columns match those in the matrix created earlier.

I used Clip(0,20) on the target value. Given an interval, values outside the interval are clipped to the interval edges. For example, if an interval of [0, 1] is specified, values smaller than 0 become 0, and values larger than 1 become 1. This way train target will be similar to the test predictions.

The test set is appended to the matrix, with 'date_block_num' set to 34.

After setting up the matrix with date block, shop ID, item ID, and item count per month, the rest of the data is merged on those features.

##  Feature Engineering using Target Lags
Given a column, such as item_cnt_month, I calculatde the shifted date_block_num and merge it back to the dataframe as an extra column, such as item_cnt_month_lag_1.

For example, if we want to add a new column with item_cnt_month_lag_6:

1. make a temporary df with the columns date_block_num, shop_id, item_id, and item_cnt_month
2. make a shifted df from the temporary df where the item_cnt_month is changed to item_cont_month_6
3. the date_block_num becomes date_block_num + 6
4. return a new dataframe where the shifted df is joined with the temporary df on date_block_num, shop_id, and item_id
Therefore, we end up with an item_cnt (for the same shop_id and item_id) where it lags behind an X number of date_block_num.

Using the target lag method created earlier, I calculated the mean item count per month and lag the values by 1, 2, 3, 6, and 12 months. 
Then I repeated the process for:
- average item count by item ID, 
- average item count by shop ID,
- average item count by item category,
- average item count by shop ID and item_category_id,
- average item count by shop ID and type_code,
- average item count by shop ID subtype,
- average item count by city code,
- average item count by item_id and city_cody,
- average item count by type_code,
- and average item count by subtype_code

I also calculated other trend features in addition to those related to average item count:
- average item price by item ID
- average item price by date block and item ID
- change in item price for 1, 2, 3, 4, 5, 6 months
- total revenue for each shop ID, by date block
- average revenue for each shop ID, by date block
- and revenue for 1, 2, 3, 4, 5, 6 months

## The Resulting Features


no.  |Column                                |Dtype  
--- |------                                |-----  
 0  |date_block_num                        |int8   
 1  |shop_id                               |int8   
 2  |item_id                               |int16  
 3  |item_cnt_month                        |float16
 4  |city_code                             |int8   
 5  |item_category_id                      |int8   
 6  |type_code                             |int8   
 7  |subtype_code                          |int8   
 8  |item_cnt_month_lag_1                  |float16
 9  |item_cnt_month_lag_2                  |float16
 10 |item_cnt_month_lag_3                  |float16
 11 |item_cnt_month_lag_6                  |float16
 12 |item_cnt_month_lag_12                 |float16
 13 |date_avg_item_cnt_lag_1               |float16
 14 |date_item_avg_item_cnt_lag_1          |float16
 15 |date_item_avg_item_cnt_lag_2          |float16
 16 |date_item_avg_item_cnt_lag_3          |float16
 17 |date_item_avg_item_cnt_lag_6          |float16
 18 |date_item_avg_item_cnt_lag_12         |float16
 19 |date_shop_avg_item_cnt_lag_1          |float16
 20 |date_shop_avg_item_cnt_lag_2          |float16
 21 |date_shop_avg_item_cnt_lag_3          |float16
 22 |date_shop_avg_item_cnt_lag_6          |float16
 23 |date_shop_avg_item_cnt_lag_12         |float16
 24 |date_cat_avg_item_cnt_lag_1           |float16
 25 |date_shop_cat_avg_item_cnt_lag_1      |float16
 26 |date_shop_type_avg_item_cnt_lag_1     |float16
 27 |date_shop_subtype_avg_item_cnt_lag_1  |float16
 28 |date_city_avg_item_cnt_lag_1          |float16
 29 |date_item_city_avg_item_cnt_lag_1     |float16
 30 |date_type_avg_item_cnt_lag_1          |float16
 31 |date_subtype_avg_item_cnt_lag_1       |float16
 32 |delta_price_lag                       |float16
 33 |delta_revenue_lag_1                   |float16
 34 |month                                 |int8   
 35 |days                                  |int8   
 36 |item_shop_last_sale                   |int8

## Mange Memory

The final dataframe is pickled and all other dataframes are deleted with the garbage collector.

# Modeling

## Train Test Split
The train test split is done based on the time period. I used the 13th to 32nd months for training set, and the 33rd month for validation set. I used the 34th month for testing set.

## Model

The model I used for this project is the XGBoost regressor model. It is fit with the RMSE evaluation metric. The final prediction is clipped.

## Feature Importance

Using the XGBoost package, a graph of feature importance measured by the f score is returned showing how many times a variable was split on. In order of importance, the top three features are:
1. date of the average item count lagged by one month
2. item category ID
3. change in price lagged by one month

## Resource Conservation

In this project, memory is actively managed to conserve resources. This is important with working with big data. For long-running applications, it can cause serious problems such as memory leaks. 

Below is some example in which memory is managed in this project:

1. features are encoded with LabelEncoder
2. datatype is specified using dtype='intXX'
3. features are deleted after important information are extracted
4. used data is deleted with the gc.collect() method
