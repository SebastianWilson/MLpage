## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/SebastianWilson/MLpage/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/SebastianWilson/MLpage/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.





# CS4641-Team-44


**Introduction/Background**

Our group's area of research is **financial market analysis**. We intend to apply various machine learning techniques to understand the factors that underlie stock performance by market, season, and time period. Through this, we hope to be able to predict various market variables using past trends.

**Problem Definition**

We are using the Historical Stock Market Dataset to identify trends in "historical daily prices and volume information for US stocks and ETFs trading on NASDAQ, NYSE, and NYSE MKT" with an eye towards long term predictions.

**Methods**

Datasets: https://www.kaggle.com/borismarjanovic/price-volume-data-for-all-us-stocks-etfs
https://www.kaggle.com/timoboz/tesla-stock-data-from-2010-to-2020

**Candlestick**

One of the primary means of candlestick analysis is finding candlestick patterns that lead to predictable outcomes. One of the hardest challenges is figuring out these patterns. We can look for many outcomes for each pattern: whether the market goes up, down, maintains direction, or inverts direction. We can also use candlesticks which represent different amounts of time. As well as look for seasonal trends for certain stocks. We can use agglomerative clustering to identify clusters of candlestick patterns which result in similar movements of the market.

**Neural nets methods**

We are planning to study the performance using a typical neural network called LSMT (long short-term memory). The LSTM model is a type of recurrent neural network, which can theoretically store an unlimited amount of time information. This kind of network can not only process single data points, but also entire sequences of data. This attribute makes it a good choice when we are doing time-series predictions. The multi-layer long and short memory neural network is used as a base classifier, the data set is divided into multiple sub-data using self-sampling method set, use the sub-data set to train the base classifier, and integrate the classification of the base classifier.
As a result, the final classification result is formed, and the classification result is the fluctuation of the next trading day.


**Regression analysis**

We intend to use this technique to find correlations between the opening price, volume, closing price, daily high, and daily low. We could also find correlations between these variables for ETFs and stocks, as well as between different markets. For instance, this would tell us whether there is a correlation between ETFs traded on the NASDAQ and stocks traded on the NYSE. We could then analyze whether there exist correlations between different candlestick pattern clusters obtained through agglomerative clustering analysis. For instance, this could tell us if a highly volatile market is usually associated with a downturn. 

Our results so far can be summarized as follows:

![image](https://user-images.githubusercontent.com/82196613/114130128-e903f380-98cd-11eb-918b-3b218c9889e3.png)

The close price of a specific stock is predicted in this linear regression model. We obtained the data from a historical stock database. In the original data set, we have open price, highest price, close price, volume. There is no missing data or null values in the dataset so there is no need to do data cleaning. The close price in the dataset is considered to be the y in this model. And 3 columns are used to form the features used in the training and predicting. 

Three features are used for the training:
1. high-low-percentage-change: the percentage change of highest and lowest prices, calculated as (high-low)/low. Highest price and lowest price is very important in analyzing the behavior of a stock. We can observe the potential of the stock through the highest and lowest prices. Converting the highest and lowest price into a percentage change acts like a “normalization” step.
2. volume of the stock:  Trading volume can help an investor identify momentum in a security and confirm a trend. Study shows that there is a positive correlation between volume and stock price.
3. open price: open price is the start point in a day. Thus it acts like a base and has a great effect on the close price.

Besides these 3 features, we are planning to add “average close price in the past 5 days” to take the long-term effect into consideration. This feature is likely to reflect the overall trending of the stock in 5 trading days. Stock prices are time series data but linear regression models do not have long term memory. Thus adding this feature can make up for this flaw to some extent. 

**Clustering Analysis**

We primarily investigated whether it is possible to cluster trading metrics of individual companies at different points in time. In other words, we were looking to see if we could classify time periods into distinct classes. We first created a Gaussian Mixture Model (GMM) to over data points (which, for our dataset, consisted of a **date**, **opening price**, **daily high**, **daily low, **closing price**, and **trading volume**. We discarded the OpenInt feature because it was mostly zeros). Before processing the data, we standardized all features (ie. made their standard deviations = 1 and their means = 0) to optimize the peformance of PCA and our clustering algorithms. 

**Discussion**

There are many variables at play which need to be tweaked to perfection which may be a major hurdle in finding the right patterns. There are also many other variables that can be taken into account in the future such as weather, seasonal trends, and any major events which could heavily impact the market. We hope to find some patterns which could help analysts predict the state of the market. The advantage of using machine learning is being able to go through a large number of data and catch patterns which may not be easily seen by a human eye.

**References**

Bow, C. (2019, November 15). An introduction to regression analysis for marketers. Medium. https://blog.markgrowth.com/an-introduction-to-regression-analysis-for-marketers-e4ece9dce43a

Chen, J. (2021, February 19). Exchange Traded Fund – ETFs. Investopedia. https://www.investopedia.com/terms/e/etf.asp

Loukas, S. (2020, July 31). Time-Series Forecasting: Predicting Stock Prices Using An LSTM Model. Medium. https://towardsdatascience.com/lstm-time-series-forecasting-predicting-stock-prices-using-an-lstm-model-6223e9644a2f

Martiny, K. (2012). [PDF] Unsupervised Discovery of Significant Candlestick Patterns for Forecasting Security Price Movements. Semantic Scholar. https://www.semanticscholar.org/paper/Unsupervised-Discovery-of-Significant-Candlestick-Martiny/1bf5c5979a4548c9836c3dacb89a951ec1aee3f1
