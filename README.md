![banner](Face%20Mask%20Rating%20Prediction/img/banner.jpg)
# Amazon Face Mask Rating Prediction with Neural Network Model

Online retailers saw an unprecedented demand for face masks in the year 2020 with the rise of COVID-19. Analyzing the reviews and ratings of products using sentiment analysis with Natural Language Processing (NTLK) can help businesses understand consumer behavior and lead to better product development. 

In this machine learning project, I created a Neural Network (NN) model that will predict the star rating on a scale of 1 to 5, based on a written review from real customers on Amazon. 

This project demonstrates a complete machine learning workflow: 
- data scraping using Selenium web automation
- data cleaning and EDA with python, numpy, and pandas
- data visualization seaborn and matplotlib
- text data processing and encoding with NTLK
- neural network modeling, training, and testing with scikit-learn, keras, and tensorflow
- model evaluation using various metrics

[Summary](https://github.com/JinySong/data-engineering-projects/blob/main/Face%20Mask%20Rating%20Prediction/README.md) | [Notebooks](https://github.com/JinySong/data-engineering-projects/tree/main/Face%20Mask%20Rating%20Prediction)


![banner](Sales%20Prediction%20with%20XGBoost/img/banner.jpg)
# Sale Volume Prediction with XGBoost

The goal of this project is to work with time-series data and use XGBoost to forecast sales volume for each store. A unique aspect of the dataset is that that the list of stores and products changes every month and there are new items in the testing dataset that are not present in the training dataset.

Project workflow summary:
- process outliers
- impute missing data
- discover data duplication
- encode features
- time-series analysis
- feature engineering using target lags
- generate trend features
- modeling with XGBoost
- model evaluation

[Summary](https://github.com/JinySong/data-engineering-projects/blob/main/Sales%20Prediction%20with%20XGBoost/README.md) | [Notebook](https://github.com/JinySong/data-engineering-projects/blob/main/Sales%20Prediction%20with%20XGBoost/Sales%20Prediction%20with%20XGBoost.ipynb)

![banner house price](House%20Price%20Prediction%20with%20Stacked%20Regression/img/banner.png)

# House Price Prediction with Stacked Regression 

The goal of this notebook is to predict house prices using stacked regression models.

Project workflow summary:
- process outliers
- process missing data, impute values from other features
- perform logarithmic transformation on skewed data
- shuffle and splitting the data for training, validation, and testing
- produce base models using lasso regression, elastic net regression, kernel ridge regression, gradient boosting regression, XGBoost, and LightGMB
- stacking models using the meta-model method where out-of-fold predictions made on the holdout dataset are used as training for a meta-model. 
- using root mean squared log error to evaluate results, which is more robust to outliers compared to traditional RMSE.

[Summary](House%20Price%20Prediction%20with%20Stacked%20Regression/README.md) |
[Notebook](House%20Price%20Prediction%20with%20Stacked%20Regression/House%20Price%20Prediction%20with%20Stacked%20Regression.ipynb)

![cc banner](Credit%20Card%20Fraud%20Detection%20with%20CNN/img/cc_banner.jpg)

# Credit Card Fraud Detection with CNN


[Summary](Credit%20Card%20Fraud%20Detection%20with%20CNN/README.md) | 
[Notebook](Credit%20Card%20Fraud%20Detection%20with%20CNN/Credit%20Card%20Fraud%20Detection%20with%20CNN.ipynb)
