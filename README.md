# CS4641-Team-44


**Introduction/Background**

Our group's area of research is **financial market analysis**. We intend to apply various machine learning techniques to discover the factors that underlie stock performance by market, season, and time period. Through this, we hope to be able to predict various market variables using past trends.

**Problem Definition**

We are using the Historical Stock Market Dataset to identify trends in "historical daily prices and volume information for US stocks and ETFs trading on NASDAQ, NYSE, and NYSE MKT" with an eye towards long term predictions. 

**Methods**

Datasets: https://www.kaggle.com/borismarjanovic/price-volume-data-for-all-us-stocks-etfs
https://www.kaggle.com/timoboz/tesla-stock-data-from-2010-to-2020

**Agglomerative Hierarchical Clustering**

One of the primary means of candlestick analysis is finding candlestick patterns that lead to predictable outcomes. One of the hardest challenges is figuring out these patterns. We can look for many outcomes for each pattern: whether the market goes up, down, maintains direction, or inverts direction. We can also use candlesticks which represent different amounts of time. As well as look for seasonal trends for certain stocks. We can use agglomerative clustering to identify clusters of candlestick patterns which result in similar movements of the market. 
To make an algorithm that can find them, we first need to represent the data in an organized and understandable manner. One of the primary ways is to take the opening, high, low, and closing (OHLC) price points for the day. A great way to represent this is to take the high, low and closing prices relative to the opening price. This reduces the number of inputs to 3 instead of 4 since we can ignore the opening price. We can also represent the position of one candle to another by comparing the midpoints of the high and low values for each day. The amount of candlesticks in each pattern can be a variable we can change with a range: n > 2. The last piece of information we need is the general trend of the market before and after the pattern which can be found by comparing the midpoints of n candlesticks before and after the pattern.

Each datapoint will be a pattern P = (OHLC1, OHLC2, OHLC3, … midDist(1, 2), midDist(2, 3) ….)(1). We can use these values to find the euclidean distance between patterns and use an agglomerative algorithm to form the hierarchical cluster. Then we must query this structure to find clusters which result in a similar outcome. 

**The Dataset**
*https://www.kaggle.com/timoboz/tesla-stock-data-from-2010-to-2020*
This is a .csv file while contains data for the Tesla stock in the following format:
Original Data:
         
         Date           Open       High   Low        Close      Adj Close  Volume
         
         0  2010-06-29  19.000000  25.00  17.540001  23.889999  23.889999  18766300
         1  2010-06-30  25.790001  30.42  23.299999  23.830000  23.830000  17187100
         2  2010-07-01  25.000000  25.92  20.270000  21.959999  21.959999   8218800
         3  2010-07-02  23.000000  23.10  18.709999  19.200001  19.200001   5139800
         4  2010-07-06  20.000000  20.00  15.830000  16.110001  16.110001   6866900




Data cleaning

First, we removed the unnecessary columns from our data. This includes Adjusted Close and Volume columns. Volume may be added to the equation in following versions. Then, we added a column for the positional difference between the candlesticks by comparing the difference in midpoints. We changed the High, Low, and close values to ratios over Open price. After that, we removed the dates as we don’t really have the use for them. The dataset now has this format:

         High         Low       Close     midDiff
         0  1.315789  0.923158  1.257368  0.000000
         1  1.179527  0.903451  0.924002  5.589999
         2  1.036800  0.810800  0.878400 -3.765000
         3  1.004348  0.813478  0.834783 -2.190000
         4  1.000000  0.791500  0.805500 -2.990000

This dataset still needs to be processed so we can represent multiple days’ worth of candlesticks as one datapoint. We cumulate multiple days worth of data to form a pattern which serves as a single datapoint for our algorithm. 

            h0        l0        c0        h1        ...  c2        d1        d2          trend
         0  1.315789  0.923158  1.257368  1.179527  ...  0.878400  5.589999 -3.765000    0.0
         1  1.179527  0.903451  0.924002  1.036800  ...  0.834783 -3.765000 -2.190000   -1.0
         2  1.036800  0.810800  0.878400  1.004348  ...  0.805500 -2.190000 -2.990000   -1.0
         3  1.004348  0.813478  0.834783  1.000000  ...  0.963415 -2.990000 -2.110000   -1.0
         4  1.000000  0.791500  0.805500  1.014024  ...  1.081784 -2.110000  0.740001    0.0

With the dataset finalized, we can apply the agglomerative hierarchical clustering algorithm using the euclidean distance formula to form our dendrogram.
We can now query this structure to find patterns which are similar to each other and result in the same outcome (direction of the market after pattern). To find the direction of the market after any given pattern we can simply check the next day’s midpoint in comparison to the last candlestick in the pattern using the finalized dataframe. 

Using a tree structure we associated each sub-dendrogram (cluster) with its corresponding patterns and the reliability of the outcomes(# patterns with the same outcome / total # of patterns in the cluster). Now, given a new pattern, we find the most similar pattern in our dataframe and check to see if it is associated with any clusters that have a high reliability. It is also important to consider the amount of patterns in the sub-dendrogram since dendrograms with high reliability and low number of patterns may still not be accurate. 

Using the first 1000 patterns for the AHC we find that there are 3 clusters that have over 70% reliability and 30+ associated patterns. 
For further testing, we took the next 900 data points and searched for clusters with similar patterns associated with 70%+ reliability and 15+ associated patterns. Out of 900 there were 7 such instances. In these instances the algorithm correctly predicted the direction of the mark 85% of the times. 

There are many variables at play and plenty of room for optimization. Moving forward we would like to take Volume into account with the other metrics. We will also optimize the reliability and number of patterns required for the cluster to marked as a valid candidate to make predictions.



**Neural nets methods**

We are planning to study the performance using a typical neural network called LSMT (long short-term memory). The LSTM model is a type of recurrent neural network, which can theoretically store an unlimited amount of time information. This kind of network can not only process single data points, but also entire sequences of data. This attribute makes it a good choice when we are doing time-series predictions. The multi-layer long and short memory neural network is used as a base classifier, the data set is divided into multiple sub-data using self-sampling method set, use the sub-data set to train the base classifier, and integrate the classification of the base classifier.
As a result, the final classification result is formed, and the classification result is the fluctuation of the next trading day.


**Regression analysis**

We intend to use this technique to find correlations between the opening price, volume, closing price, daily high, and daily low. We could also find correlations between these variables for ETFs and stocks, as well as between different markets. For instance, this would tell us whether there is a correlation between ETFs traded on the NASDAQ and stocks traded on the NYSE. We could then analyze whether there exist correlations between different candlestick pattern clusters obtained through agglomerative clustering analysis. For instance, this could tell us if a highly volatile market is usually associated with a downturn.

Our results so far can be summarized as follows:

The close price of a specific stock is predicted in this linear regression model. We obtained the data from a historical stock database. In the original data set, we have open price, highest price, close price, volume. There is no missing data or null values in the dataset so there is no need to do data cleaning. The close price in the dataset is considered to be the y in this model. And 3 columns are used to form the features used in the training and predicting. 

3 features are used for the training: 
high-low-percentage-change: the percentage change of highest and lowest prices, calculated as (high-low)/low. Highest price and lowest price is very important in analyzing the behavior of a stock. We can observe the potential of the stock through the highest and lowest prices. Converting the highest and lowest price into a percentage change acts like a “normalization” step.
volume of the stock:  Trading volume can help an investor identify momentum in a security and confirm a trend. Study shows that there is a positive correlation between volume and stock price.
open price: open price is the start point in a day. Thus it acts like a base and has a great effect on the close price.
Besides these 3 features, we are planning to add “average close price in the past 5 days” to take the long-term effect into consideration. This feature is likely to reflect the overall trending of the stock in 5 trading days. Stock prices are time series data but linear regression models do not have long term memory. Thus adding this feature can make up for this flaw to some extent. 


**Discussion**

There are many variables at play which need to be tweaked to perfection which may be a major hurdle in finding the right patterns. There are also many other variables that can be taken into account in the future such as weather, seasonal trends, and any major events which could heavily impact the market. We hope to find some patterns which could help analysts predict the state of the market. The advantage of using machine learning is being able to go through a large number of data and catch patterns which may not be easily seen by a human eye.

**References**

Bow, C. (2019, November 15). An introduction to regression analysis for marketers. Medium. https://blog.markgrowth.com/an-introduction-to-regression-analysis-for-marketers-e4ece9dce43a

Chen, J. (2021, February 19). Exchange Traded Fund – ETFs. Investopedia. https://www.investopedia.com/terms/e/etf.asp

Loukas, S. (2020, July 31). Time-Series Forecasting: Predicting Stock Prices Using An LSTM Model. Medium. https://towardsdatascience.com/lstm-time-series-forecasting-predicting-stock-prices-using-an-lstm-model-6223e9644a2f

Martiny, K. (2012). [PDF] Unsupervised Discovery of Significant Candlestick Patterns for Forecasting Security Price Movements. Semantic Scholar. https://www.semanticscholar.org/paper/Unsupervised-Discovery-of-Significant-Candlestick-Martiny/1bf5c5979a4548c9836c3dacb89a951ec1aee3f1


