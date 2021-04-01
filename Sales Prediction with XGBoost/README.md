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


## Resource Conservation

In this project, memory is actively managed to conserve resources. This is important with working with big data. For long-running applications, it can cause serious problems such as memory leaks. 

Below is some example in which memory is managed in this project:

1. features are encoded with LabelEncoder
2. datatype is specified using dtype='intXX'
3. features are deleted after important information are extracted
4. used data is deleted with the gc.collect() method
