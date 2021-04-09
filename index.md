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

The goal of clustering analysis is to find out whether there are certain attractor states that individual companies or the market at large tends to fluctuate between. We did this by examining the natural features of our dataset as well as the various engineered features. We analyzed this by looking at how features vary over time, rather than across data points.

We primarily investigated whether it is possible to cluster trading metrics of individual companies at different points in time. In other words, we were looking to see if we could classify time periods into distinct classes. We first created a Gaussian Mixture Model (GMM) to over data points (which, for our dataset, consisted of a **date**, **opening price**, **daily high**, **daily low**, **closing price**, and **trading volume**. We discarded the OpenInt feature because it was mostly zeros). Before processing the data, we standardized all features (ie. made their standard deviations = 1 and their means = 0) to optimize the peformance of PCA and our clustering algorithms.

For our preliminary analysis, we examined a single stock - Agilent Technologies (stock A). We first used k-means to determine whether it was even possible to cluster our data meaningfully over time. Using the elbow method, checking up to k=100, we found that there did indeed seem to be a distinct number of clusters, with the “bend” occurring around k=7. There were three possible reasons for this:
This was an idiosyncrasy of stock A, and you couldn’t, in general, classify time periods of market patterns.
Stock A was following larger market trends (which consist of distinct “periods” of different combinations of feature states like “high volume, high growth”, “low volume, high growth”, and so on.)
Individual companies, rather than the market at large, shuffle through these attractor states independently.
The second hypothesis is the strongest among these - not only would we have to find distinct states of individual companies, but we would have to show that these states are correlated across different companies.
Shown below are the sum of squared error (within cluster) values for k’s up to 100. Note the location of the bend.


And here are the clusters visualized when k=7.

Compare this to the clustering for k=5, 6, 8, and 8 respectively.

Notice how much more the distinct the boundaries are between states clusters when comparing opening price to time. Since we speculated that opening price would be highly correlated with closing price, daily, high, and daily low, we only plotted opening price to proxy these other features. We can also observe how clusters appear when plotting volume.
Shown below are visualizations for k=6, 7, and 8 respectively when plotting volume instead of opening price.




Once again, notice how sharp the boundaries between clusters are when k=7.
We applied this same method of analysis to a sample ETF - AdvisorShares Dorsey Wright (AADR) - to determine if this pattern holds for ETFs (which would indicate that asset metrics are being determined by the market at large.)

Shown below are the sum of squared error (within cluster) values for k’s up to 100. 
From these, we determined k=5 to be best. The corresponding scatterplot is shown below.


Now, let’s observe what happened for the time vs. volume plot when k=5. The clusters are more diffused across time.

This gives more insight into what might have occurred. In particular, the reason why the clusters seem to overlap in time is because days with low trading volume were interspersed with days with very large trading volumes. In fact, when we reduce the number of clusters to 2, this becomes the primary distinction between clusters. The plot for k=2 is shown below.


For the most part, this plot shows less distinct clustering along the time axis than we did for the stock A, and more volatility in trading volume. We only see distinct clustering toward the right side of the plot, where there seems to be less market volatility. 
So far, we have only examined the raw data of two assets. Before moving on to sampling different assets, we will make sure we haven’t overlooked any other possible patterns in the data. One possible oversight is that we may be including many redundant features in our clustering analysis. As mentioned before, it appears that the price and volume are the latent variables driving the variance in data. Therefore, we will perform PCA to reduce the number of dimensions to 2. This has the convenient side effect of being easily visualizable.
We first apply PCA to stock A. Using the elbow method, we find the best k to be somewhere from 13 to 16. The bend is now more pronounced. 
Here is the plot for k=13

Notice how the data seems to be oriented diagonally. This is likely the orientation of the original time axis. When we see overlapping data, this is because those data have been broken into upper and lower clusters. Presumably, these new features have something to do with volume and price. Compare to the plot for k=7, which we found to neatly separate clusters along the time axis.

This data implies that there aren’t distinct attractor states - otherwise we would be seeing at least two different loci of clustering when reducing to principal components. Does this hold for ETFs and other stocks?
For our sample ETF, AADR, an interesting pattern emerges. While there are two distinct masses of points, there seems to be a dense mass within a larger, sparser mass. Here is the plot when k=2 to illustrate this.

The dense region, once again, is likely time. However, many points stray far away from the dense region, and where they do, they seem to be fairly evenly spread out. This implies that there are two attractor states for ETFs (at least the AADR ETF): very low price or trading volume or essentially randomized price + trading volume. As demonstrated by Figure X, this effect is likely driven entirely by trading volume. Further investigations will seek to correlate these two categories of ETF states with other financial metrics. Contrast this with stocks, which had less volatile trading volume. Therefore, time was a relatively greater source of variance for stocks, leading to clustering to occur along the time axis. 

Another possible confounder is our initial inclusion of time when clustering. After all, we are looking for attractor states, and there is clearly no such thing as an “attractor time”. Therefore, we observed what happens when removing the time feature before performing dimensionality reduction using PCA.
Here are a few examples of other assets clusters when time is removed and we perform dimensionality reduction (k=2 for each):


aa












aaba













aaap











aaxj












acim













actx


It appears that some assets seem to have certain attractor states. Particularly, the stock AA seems to have a random feature 1 + middling feature 2 attractor state and a random feature 2 + low feature 1 attractor state. More interestingly, different assets seem to have a negative or positive correlation between feature 1 and feature 2. Once again, these are presumably proxies for price and volume. In the next stage of clustering analysis, we will try to find clusters of assets according to whether there is a negative or positive correlation between features, and we will attempt to see if these clusters map onto other patterns we find. 
We finally attempted to apply dimensionality reduction and then GMM to the rates of change of different features. We did this by constructing an array of the differences between subsequent points in time. In nearly all cases, the data was spread in approximately this fashion:

When you remove time, the spread changes to look like this:

Neither of these plots were especially illuminating. However, patterns of rates of change remain a potential topic for future investigation.

Clustering analysis has yielded two main takeaways:
There are attractor states that different assets tend to settle into. For instance, many days involve a trading volume of 0, while others have randomly distributed trading volume. If we can cluster days with low trading volume and remove them from our dataset, this could yield higher fidelity results in later analyses. Further cross-data-point analysis needs to be conducted to determine whether there are differences between ETF and stock behavior.
Dense clusters at the core of a plot tend to have a negative or positive correlation relative to the principal axes. This possibly gives another way in which to classify objects.
Ultimately, the main reason for looking for trends over time is to be able to compare time series behavior between different assets. Clustering analysis is suggestive of many patterns and ways of categorizing behavior at different points in time, but the main benefit of the takeaways is that they give another way of comparing assets. Further clustering analysis will focus on finding clusters of assets rather than asset states.


**Discussion**

There are many variables at play which need to be tweaked to perfection which may be a major hurdle in finding the right patterns. There are also many other variables that can be taken into account in the future such as weather, seasonal trends, and any major events which could heavily impact the market. We hope to find some patterns which could help analysts predict the state of the market. The advantage of using machine learning is being able to go through a large number of data and catch patterns which may not be easily seen by a human eye.

**References**

Bow, C. (2019, November 15). An introduction to regression analysis for marketers. Medium. https://blog.markgrowth.com/an-introduction-to-regression-analysis-for-marketers-e4ece9dce43a

Chen, J. (2021, February 19). Exchange Traded Fund – ETFs. Investopedia. https://www.investopedia.com/terms/e/etf.asp

Loukas, S. (2020, July 31). Time-Series Forecasting: Predicting Stock Prices Using An LSTM Model. Medium. https://towardsdatascience.com/lstm-time-series-forecasting-predicting-stock-prices-using-an-lstm-model-6223e9644a2f

Martiny, K. (2012). [PDF] Unsupervised Discovery of Significant Candlestick Patterns for Forecasting Security Price Movements. Semantic Scholar. https://www.semanticscholar.org/paper/Unsupervised-Discovery-of-Significant-Candlestick-Martiny/1bf5c5979a4548c9836c3dacb89a951ec1aee3f1
