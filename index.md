
# CS4641 Team 44


**Introduction/Background**

Our group's area of research is **financial market analysis**. We applied various machine learning techniques to identify and understand the factors that underlie asset performance - for instance, time period, trading volume, and the performance of related assets. We were able to predict various market variables using past trends alone.

**Problem Definition**

We are using the Historical Stock Market Dataset to identify trends in "historical daily prices and volume information for US stocks and ETFs trading on NASDAQ, NYSE, and NYSE MKT" with an eye towards long term predictions.

**Methods**

Datasets: https://www.kaggle.com/borismarjanovic/price-volume-data-for-all-us-stocks-etfs
https://www.kaggle.com/timoboz/tesla-stock-data-from-2010-to-2020


**Candlestick Analysis/Agglomerative Hierarchical Clustering**

One of the primary means of candlestick analysis is finding candlestick patterns that lead to predictable outcomes. One of the hardest challenges is figuring out these patterns. We can look for many outcomes for each pattern: whether the market goes up, down, maintains direction, or inverts direction. We can also use candlesticks which represent different amounts of time, as well as look for seasonal trends for certain stocks. We can use agglomerative clustering to identify clusters of candlestick patterns which result in similar movements of the market. 

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

First, we removed the unnecessary columns from our data. This includes Adjusted Close and Volume columns. Then, we added a column for the positional difference between the candlesticks by comparing the difference in midpoints. We changed the High, Low, and close values to ratios over Open price. After that, we removed the dates as we don’t really have the use for them. The dataset now has this format:

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

![Figure 1](https://cdn.discordapp.com/attachments/836397263197700126/836397991493238845/unknown.png)

We can now query this structure to find patterns which are similar to each other and result in the same outcome (direction of the market after pattern). To find the direction of the market after any given pattern we can simply check the next day’s midpoint in comparison to the last candlestick in the pattern using the finalized dataframe. 

Using a tree structure we associated each sub-dendrogram (cluster) with its corresponding patterns and the reliability of the outcomes(# patterns with the same outcome / total # of patterns in the cluster). Now, given a new pattern, we find the most similar pattern in our dataframe and check to see if it is associated with any clusters that have a high reliability. It is also important to consider the amount of patterns in the sub-dendrogram since dendrograms with high reliability and low number of patterns may still not be accurate. 

Using the first 1000 patterns for the AHC we find that there are 3 clusters that have over 70% reliability and 30+ associated patterns. 
For further testing, we took the next 900 data points and searched for clusters with similar patterns associated with 70%+ reliability and 15+ associated patterns. Out of 900 there were 7 such instances. In these instances the algorithm correctly predicted the direction of the mark 85% of the times. 

This scatterplot shows the accuracy rate of our AI for 30 stocks. Looking at the scatterplot, the prediction rate is not high enough to base any real investments off of. However, this AI can be used by analysts as a supplementary tool for their analysis. Depending on how accurately the algorithm performs for a certain stock, we can tell if the stock tends to follow similar patterns as it did in the past. With the stock that has 70% accuracy, it is safe to say that it does tend to follow similar patterns whereas the stock that has 40% accuracy can be seen as more volatile. There are also clear outliers near stocks in which many patterns could not be detected. If the stock has high accuracy and more detected patterns, it is a more reliable stock. The analysts can leverage these numbers and include larger datasets to help them predict the outcome of the market for any particular stock.

![Figure 1](https://cdn.discordapp.com/attachments/836397263197700126/836692536877121556/unknown.png)

There are many variables at play and plenty of room for optimization. Moving forward we would like to take Volume into account with the other metrics. We will also optimize the reliability and number of patterns required for the cluster to marked as a valid candidate to make predictions.


**Regression analysis**

We intend to use this technique to find correlations between the opening price, volume, closing price, daily high, and daily low. We could also find correlations between these variables for ETFs and stocks, as well as between different markets. For instance, this would tell us whether there is a correlation between ETFs traded on the NASDAQ and stocks traded on the NYSE. We could then analyze whether there exist correlations between different candlestick pattern clusters obtained through agglomerative clustering analysis. For instance, this could tell us if a highly volatile market is usually associated with a downturn. 

Our results so far can be summarized as follows:

![image](https://user-images.githubusercontent.com/82196613/114130128-e903f380-98cd-11eb-918b-3b218c9889e3.png)

The close price of a specific stock is predicted in this linear regression model. We obtained the data from a historical stock database. In the original data set, we have open price, highest price, close price, volume. There is no missing data or null values in the dataset so there is no need to do data cleaning. The close price in the dataset is considered to be the y in this model. And 3 columns are used to form the features used in the training and predicting. 

Three features are used for the training:
1. high-low-percentage-change: the percentage change of highest and lowest prices, calculated as (high-low)/low. Highest price and lowest price is very important in analyzing the behavior of a stock. We can observe the potential of the stock through the highest and lowest prices. Converting the highest and lowest price into a percentage change acts like a “normalization” step.
2. volume of the stock:  Trading volume can help an investor identify momentum in a security and confirm a trend. Study shows that there is a positive correlation between volume and stock price.
3. open price: open price is the start point in a day. Thus it acts like a base and has a great effect on the close price.

Besides these 3 features, one could add “average close price in the past 5 days” to take the long-term effect into consideration. This feature is likely to reflect the overall trending of the stock in 5 trading days. Stock prices are time series data but linear regression models do not have long term memory. Thus adding this feature can make up for this flaw to some extent. 




**Clustering by Correlation Coefficients**


In this section, we attempt to group assets according to their correlation with other assets over time. Whereas candlestick analysis aimed to make predictions, this section will focus on understanding the structure of the market.

We started by making the following modifications to the data: 1) We took a subset of the stocks in the dataset 2) We computed the moving average at every point in time to smooth out noise. (ie. we applied a kernel of 1’s). 3) We computed the deltas for each feature except for time, and then we divided these deltas by the values at the previous timestep. This gave us an approximation of the instantaneous percent change for each feature. 4) We standardized the values we obtained. 
We are mainly interested in finding patterns in stock prices. Therefore, in this section, we will ignore all features except for price and time. The first thing we did was test out what happens when you compare the prices of two different stocks at different points in time. In the plot below, we find that there is a weakly positive correlation between percentage price increase in two randomly selected stocks. Note that each data point represents the average price of each stock over an arbitrary ten day period. 

![image](https://user-images.githubusercontent.com/82196613/116378528-38a85180-a7e0-11eb-863a-6a0bf7865e1b.png)


This finding motivated this section’s central question: Do certain stocks predictably “behave” similarly to other stocks? And if so, can we find groups of similarly behaving stocks? This question is of particular relevance to hedging. For instance, if you wanted to reduce your risk in the market, you could simply buy stocks whose prices are negatively correlated so that your overall wins and losses will tend to balance out. We find that stock prices, despite being locally volatile, strongly correlated with certain other stock prices, even on short timescales. The below chart shows the correlation coefficients between each pair of stocks in a ten-stock sample. Once again, we are using the average price over periods of ten days.

![image](https://user-images.githubusercontent.com/82196613/116378773-6beae080-a7e0-11eb-9bef-141a894baa36.png)


 
We can conclude that it is not uncommon for stock prices to be entangled with one another. However, it remains to be seen whether this implies the existence of larger entangled groups. To invetistigate this possibility, we can cluster stocks based on a matrix of correlation coefficients using agglomerative clustering. This allows us to reorder the rows and columns in the matrix until distinct blocks of highly correlated stocks emerge. A visualization of this is given below:

![image](https://user-images.githubusercontent.com/82196613/116378828-7ad19300-a7e0-11eb-8dc7-0c89a900f3a0.png)


![image](https://user-images.githubusercontent.com/82196613/116378863-81f8a100-a7e0-11eb-9e22-be16f76f1335.png)


![image](https://user-images.githubusercontent.com/82196613/116378907-8ae97280-a7e0-11eb-9b94-3f8c762d86e7.png)


Source: https://scikit-learn.org/stable/auto_examples/bicluster/plot_spectral_coclustering.html
Shown below is a correlation coefficient heatmap for 142 stocks:

![image](https://user-images.githubusercontent.com/82196613/116379171-c08e5b80-a7e0-11eb-948f-913e8f71670e.png)



Note that this heatmap is currently unsorted. Once we use agglomerative clustering to obtain a linkage structure, we will be able to place children of the same node next to one another in the matrix. 
We have several distance metrics/linkages to choose from when performing agglomerative clustering: single, complete, average, weighted, centroid, and ward. We tested each of these on our sample. Recall that we are looking for distinct squares along the diagonal. My this measure, most linkages did not yield good results. The best performing distance metrics were ‘complete’ and ‘ward’. To demonstrate this, consider the difference between centroid and ward with kernel size (ie. the moving average) equal to 10:

![image](https://user-images.githubusercontent.com/82196613/116379269-d9970c80-a7e0-11eb-8ade-639181a7184a.png)


Figure 1: centroid matrix

![image](https://user-images.githubusercontent.com/82196613/116379397-f4698100-a7e0-11eb-8594-3ecd5a6d6330.png)


Above: ward
After testing out various values for our hyperparameters (sample size, kernel size, and distance metric), we obtained this matrix: 

![image](https://user-images.githubusercontent.com/82196613/116379472-0814e780-a7e1-11eb-95e0-76d5326a1da0.png)


Figure 2: sorted correlation matrix. Complete linkage. Kernel size = 30. Sample size = 142

In this visualization, we see a large green square in the upper lefthand corner. The stocks at each row and column that map to areas in the green square are correlated with other members of the square and less correlated with those outside of it (note that rows and columns are symmetrical in this diagram). In fact, it appears that several “patches” are distinctly red, implying that that cluster of stocks is negatively correlated with another cluster of stocks. This tells us that there are distinct groups of stocks whose behavior gives us information about the behavior of other groups of stocks. This may map onto the notion of an industry - however, it is unlikely that the hard, man-made categorizations of stocks into industries can alone capture the statistical complexity demonstrated by the above matrix. 

Several other matrices showed similar patterns of clustering. Note that the positions of the squares can change from matrix to matrix since the ordering is randomized. 

![image](https://user-images.githubusercontent.com/82196613/116379536-18c55d80-a7e1-11eb-8068-cf32b8fbe039.png)


Above: sorted correlation matrix, complete, kernel size = 1, sample size = 149

![image](https://user-images.githubusercontent.com/82196613/116379593-2844a680-a7e1-11eb-9826-afbd397d955a.png)


Above: ward, kernel size = 1, sample size = 149
After making the matrices, we made the corresponding dendrograms. These dendrograms give us the distances between different stocks as well as their cluster membership. The dendrogram corresponding to figure 1 is shown below:

![image](https://user-images.githubusercontent.com/82196613/116379661-3abee000-a7e1-11eb-84ec-e07582c964f9.png)


Compare this to the dendrogram for the matrix using centroid, kernel = 1, and sample size 149. Visually, this matrix did not produce a pattern. Unsurprisingly, its dendrogram is very imbalanced at all levels. 

![image](https://user-images.githubusercontent.com/82196613/116379713-49a59280-a7e1-11eb-8f18-f1842b485b84.png)


Clearly, different clustering methods can lead to very different results and performance. It is therefore likely that the actual boundaries between the cluster squares should look much more distinct than what was shown here. Hinting at this is the fact that we can obtain a correlation matrix of this quality for volume:

![image](https://user-images.githubusercontent.com/82196613/116379776-575b1800-a7e1-11eb-8c2c-8634cbb331bd.png)


The corresponding dendrogram is shown below: 

![image](https://user-images.githubusercontent.com/82196613/116379885-72c62300-a7e1-11eb-8920-b98a50ee8ef7.png)


In order to numerically verify our findings, we experimentally measured the goodness of our intragroup correlation coefficient matrices. Using the clustering and hyperparameters in figure 1, and making a cut in the dendrogram resulting in 6 groups, we produced a table that tells us the following (in this order): 1) the cluster number, 2) the average correlation coefficient between members of this cluster, and 3) the average correlation coefficient between members of a randomly chosen sample of equal size. This gives us an experimental benchmark. 

![image](https://user-images.githubusercontent.com/82196613/116379966-896c7a00-a7e1-11eb-9155-3b591202a6c9.png)


The results show that intracluster correlation is much higher for clusters produced by hierarchical clustering than for randomly generated clusters. This is despite obtaining the analytical result that each cluster individually wasn't much more correlated than the overall matrix was. However, the reason for this is that you cannot simply take an aveage of correlation coefficients without running into weird results. Therefore, we consider the experimental to be the strongest evidence in favor of the hypothesis.


**Further Clustering Analysis**

The goal of clustering analysis in this section is to find out whether there are certain attractor states that individual companies or the market at large tends to fluctuate between. We did this by examining the natural features of our dataset as well as various engineered features. We are now looking at how features vary over time, rather than across data points.

We primarily investigated whether it is possible to cluster trading metrics of individual companies at different points in time. In other words, we were looking to see if we could classify time periods into distinct classes, characterized by their price, volume, etc. We first created a Gaussian Mixture Model (GMM) over data points of a single stock (datapoints, in our dataset, consisted of a date, opening price, daily high, daily low, closing price, and trading volume. We discarded the OpenInt feature because it was mostly zeros). Before processing the data, we standardized all features (ie. made their standard deviations = 1 and their means = 0) to optimize the performance of PCA and our clustering algorithms.
For our preliminary analysis, we examined a single stock - Agilent Technologies (stock A). We first used k-means to determine whether it was even possible to cluster our data meaningfully over time. Using the elbow method, checking up to k=100, we found that there did indeed seem to be a distinct number of clusters, with the “bend” occurring around k=7. There were three possible reasons for this:

1. This was an idiosyncrasy of stock A, and you couldn’t, in general, classify time periods of market patterns.
2. Stock A was following larger market trends (which consist of distinct “periods” of different combinations of feature states like “high volume, high growth”, “low volume, high growth”, and so on.)
3. Individual companies, rather than the market at large, shuffle through these attractor states independently.

The second hypothesis is the strongest among these - not only would we have to find distinct states of individual companies, but we would have to show that these states are correlated across different companies.
Shown below are the sum of squared error values (within cluster) for k’s up to 100. Note the location of the bend.

![image](https://user-images.githubusercontent.com/82196613/114153647-89b5db80-98ed-11eb-9418-d9025dcd7572.png)

And here are the clusters visualized when k=7.

![image](https://user-images.githubusercontent.com/82196613/114153702-9a665180-98ed-11eb-9561-35c6d060b844.png)

*above: k=7, time vs open*

Compare this to the clustering for k=5, 6, and 8 respectively.

![image](https://user-images.githubusercontent.com/82196613/114153753-a8b46d80-98ed-11eb-9e81-9c014e282696.png)

*above: k=5, time vs open*

![image](https://user-images.githubusercontent.com/82196613/114153787-b36f0280-98ed-11eb-9a8c-c19e2c8d6b6a.png)

*above: k=6, time vs open*

![image](https://user-images.githubusercontent.com/82196613/114153830-bec22e00-98ed-11eb-8f4e-5c44f2b5fdce.png)

*above: k=8, time vs open*

Notice how much more distinct the boundaries are between clusters when comparing opening price to time. Since we speculate that opening price would be highly correlated with closing price, daily, high, and daily low, we only plotted opening price to proxy these other features. We can also observe how clusters appear when plotting volume.
Shown below are visualizations for k=6, 7, and 8 respectively when plotting volume instead of opening price.

![image](https://user-images.githubusercontent.com/82196613/114153869-cd104a00-98ed-11eb-9e87-5b6632acae0a.png)

*above: k=6, time vs volume*

![image](https://user-images.githubusercontent.com/82196613/114153908-d7cadf00-98ed-11eb-91c1-c9de374c65f5.png)

*above: k=7, time vs volume*

![image](https://user-images.githubusercontent.com/82196613/114153950-e2857400-98ed-11eb-8537-b5c86349c111.png)

*above: k=8, time vs volume*


Once again, notice how sharp the boundaries between clusters are when k=7.
We applied this same method of analysis to a sample ETF - AdvisorShares Dorsey Wright (AADR) - to determine if this pattern holds for ETFs (which would indicate that asset metrics are being determined by the market at large.)

Shown below are the sum of squared error (within cluster) values for k’s up to 100. 

![image](https://user-images.githubusercontent.com/82196613/114154017-f4671700-98ed-11eb-9ee3-56d4f283b72f.png)


From these, we determined k=5 to be best. The corresponding scatterplot is shown below.

![image](https://user-images.githubusercontent.com/82196613/114154067-00eb6f80-98ee-11eb-9be8-7989417d89db.png)

*above: k=5, time vs open*

Now, let’s observe what happened for the time vs. volume plot when k=5. The clusters are more diffused across time.

![image](https://user-images.githubusercontent.com/82196613/114154109-0d6fc800-98ee-11eb-9573-b1567da0038b.png)

*above: k=5, time vs volume*


This gives more insight into what might have occurred. In particular, the reason why the clusters seem to overlap in time is because days with low trading volume were interspersed with days with very large trading volumes. In fact, when we reduce the number of clusters to 2, this becomes the primary distinction between clusters. The plot for k=2 is shown below.

![image](https://user-images.githubusercontent.com/82196613/114154200-21b3c500-98ee-11eb-8566-0f4bc0f527b6.png)

*above: Figure 1, k=2, time vs volume*


For the most part, this plot shows less distinct clustering along the time axis than did stock A, as well as more volatility in trading volume. We only see distinct clustering toward the right side of the plot, where there seems to be less market volatility. 
So far, we have only examined the raw data of two assets. Before moving on to sampling different assets, we will make sure we haven’t overlooked any other possible patterns in the data. One possible oversight is that we may be including many redundant features in our clustering analysis. As mentioned before, it appears that the price and volume are the latent variables driving the variance in data. Therefore, we will perform PCA to reduce the number of dimensions to 2. This has the convenient side effect of being easily visualizable.
We first apply PCA to stock A. Using the elbow method, we find the best k to be somewhere from 13 to 16. The bend is now more pronounced. 

![image](https://user-images.githubusercontent.com/82196613/114154309-40b25700-98ee-11eb-94d5-ad330bf1aa41.png)

Below is the plot for k=13

![image](https://user-images.githubusercontent.com/82196613/114154353-4d36af80-98ee-11eb-8be7-b144430db4fe.png)

*above: k=13, PCA*


Notice how the data seems to be oriented diagonally. This is likely the orientation of the original time axis. When we see overlapping clusters, this is because the data have been broken into upper and lower clusters, with respect to some other feature. Presumably, the new features have something to do with volume and price, as per our previous speculation. Compare to the plot for k=7, which we found to neatly separate clusters along the time axis.

![image](https://user-images.githubusercontent.com/82196613/114154378-57f14480-98ee-11eb-9d6e-274e74a11ba2.png)

*above: k=7, PCA*


The data imply that there aren’t distinct attractor states for this stock - otherwise we would be seeing at least two different loci of clustering when reducing to principal components. Does this hold for ETFs and other stocks?
For our sample ETF, AADR, an interesting pattern emerges. While there are two distinct masses of points, there seems to be a dense mass within a larger, sparser mass. Here is the plot when k=2 to illustrate this.

![image](https://user-images.githubusercontent.com/82196613/114154434-67708d80-98ee-11eb-95a1-037375ea2bcc.png)

*above: k=2, PCA*


The dense region, once again, is likely time. However, many points stray far away from the dense region, and where they do, they seem to be fairly evenly spread out. This implies that there are two attractor states for ETFs (at least the AADR ETF): very low price or trading volume or essentially randomized price + trading volume. As demonstrated by Figure 1, this effect is likely driven entirely by trading volume. Further investigations will seek to correlate these two categories of ETF states with other financial metrics. Contrast this with stock A, which had less volatile trading volume. This made time a relatively greater source of variance for stock A, leading to clustering to occur in sequence along the time axis. 

Another possible confounder is our initial inclusion of time when clustering. After all, we are looking for attractor states, and there is clearly no such thing as an attractor time. Therefore, we observed what happens when removing the time feature before performing dimensionality reduction using PCA.
Here are a few examples of other assets clusters when time is removed and we perform dimensionality reduction (k=2 for each):


![image](https://user-images.githubusercontent.com/82196613/114154475-71928c00-98ee-11eb-94cd-c9596f6b461c.png)

*above: AA - stock, k=2, PCA, timeless*


![image](https://user-images.githubusercontent.com/82196613/114154544-853df280-98ee-11eb-8e5d-aa292f19adb1.png)

*above: AABA - stock, k=2, PCA, timeless*


![image](https://user-images.githubusercontent.com/82196613/114154601-9424a500-98ee-11eb-8cfa-377fc7fdeb52.png)

*above: AAAP - stock, k=2, PCA, timeless*


![image](https://user-images.githubusercontent.com/82196613/114154713-b3233700-98ee-11eb-94c1-e2b44189fada.png)

*above: AAXJ - ETF, k=2, PCA, timeless*


![image](https://user-images.githubusercontent.com/82196613/114154759-c1715300-98ee-11eb-88e3-1f0063c938b5.png)

*above: ACIM - ETF, k=2, PCA, timeless*


![image](https://user-images.githubusercontent.com/82196613/114154833-d3eb8c80-98ee-11eb-8e00-b4d2035d0dac.png)

*above: ACTX - ETF, k=2, PCA, timeless*


It appears that some assets seem to have certain attractor states. Particularly, the stock AA seems to have (a random feature 1 + middling feature 2) attractor state and (a random feature 2 + low feature 1) attractor state. More interestingly, different assets seem to have a negative or positive correlation between feature 1 and feature 2 when looking only at the dense region. In the next stage of clustering analysis, we will try to find clusters of assets according to whether there is a negative or positive correlation between features, and we will attempt to see if these clusters map onto other patterns we find. 
We finally attempted to apply dimensionality reduction and then GMM to the rates of change of different features. We did this by constructing an array of the differences between subsequent points in time. In nearly all cases, the data was spread in approximately this fashion:

![image](https://user-images.githubusercontent.com/82196613/114154894-e5349900-98ee-11eb-937f-fc1a49c3e86f.png)

*above: randomly chosen k, rates of change, PCA*


When you remove time, the spread changes to look like this:

![image](https://user-images.githubusercontent.com/82196613/114154935-eebe0100-98ee-11eb-9f0d-da7baa42b49f.png)

*above: randomly chosen k, rates of change, PCA, timeless*


Neither of these plots were especially illuminating. However, patterns of rates of change remain a potential topic for future investigation and have proven to be useful in certain contexts (see: previous section).

This analysis has yielded three main takeaways:
1. There are sometimes attractor states that different assets tend to settle into. For instance, many days involve a trading volume of 0, while others have randomly distributed trading volume. If we can cluster days with low trading volume and remove them from our dataset, this could yield higher fidelity results in later analyses. Further cross-data-point analysis needs to be conducted to determine whether there are differences between ETF and stock behavior.
2. Dense clusters at the core of a plot tend to have a negative or positive correlation relative to the principal axes. This possibly gives another way in which to classify objects.
3. Classifying data by time period (ideally to identify patterns of overall market behavior) is difficult using only clustering analysis.


Ultimately, the main reason for looking for trends over time is to be able to compare time series behavior between different assets. While clustering analysis does allow us to categorize behavior at different points in time for a single asset, the main benefit is that it gives another way of comparing different assets - by classifying long term behavior over time. Categorization by period (depression, rececssion, etc.), can furthermore allow you make predictions about what may come next.



**Discussion**

There are many variables at play which need to be tweaked to perfection which may be a major hurdle in finding the right patterns. There are also many other variables that can be taken into account in the future such as weather, seasonal trends, and any major events which could heavily impact the market. We hope to find some patterns which could help analysts predict the state of the market. In a sense, prediction and analysis are complements - whereas analysis derives its conclusions from the bottom up, prediction requires you to take larger patterns into account and simply try out what works. However, both are central to making full use of machine learning techniques.


**Conclusion**

With the Agglomerative Hierarchical Clustering method, we were able to find 3 clusters that had 70%+ reliability and 30+ associated patterns. Although this is a good rate, due to the absence of volume variable in our method, we weren't able to reliably predict any real investment. However, analysts could use this data as a supplement to analyses. Moving forward, we hope to add volume to our method, which will help us predict future trend better.

Regression analysis was sufficient for predicting extremely short term trends, but one of its drawbacks was its inability to take long term trends into account. In other words, it's "memory" is too short. Future attempts at predictions via this method should probably incorporate a LSTM neural network, which is specialized for this problem. Furthermore, neural nets allow you to more easily approximate nonlinear functions, which is a class that without a doubt includes the stock market. 

Clustering was a fruitful avenue of investigation. We were able to conclude that distinct groups, perhaps analogous to what we normally think of as industries, exist in the stock market. These groups are characterized by similar behavior - when one member increases in price, other members are mor likely as well. Furthermore, we were able to adapt agglomerative clustering for use in clustering stocks by their correlation over _time_, not just position. We also made headway into the question of whether financial history is cyclical (or at lesat predictable at a large scale). We found out that there are indeed weak attractor states, namely, the tendencies to either remain at price level around 0 or to erratically fluctuate. Both of these clustering investigatoins are relavent to hedging. While knowing that company A must go down when company B goes up doesn't tell you who to invest in, it does tell how to invest assuming you want to minimize risk.


**References**

Bow, C. (2019, November 15). An introduction to regression analysis for marketers. Medium. https://blog.markgrowth.com/an-introduction-to-regression-analysis-for-marketers-e4ece9dce43a

Chen, J. (2021, February 19). Exchange Traded Fund – ETFs. Investopedia. https://www.investopedia.com/terms/e/etf.asp

Loukas, S. (2020, July 31). Time-Series Forecasting: Predicting Stock Prices Using An LSTM Model. Medium. https://towardsdatascience.com/lstm-time-series-forecasting-predicting-stock-prices-using-an-lstm-model-6223e9644a2f

Martiny, K. (2012). [PDF] Unsupervised Discovery of Significant Candlestick Patterns for Forecasting Security Price Movements. Semantic Scholar. https://www.semanticscholar.org/paper/Unsupervised-Discovery-of-Significant-Candlestick-Martiny/1bf5c5979a4548c9836c3dacb89a951ec1aee3f1
