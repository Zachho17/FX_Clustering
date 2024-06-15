# Foreign Exchange Clustering and Market Themes Identification

## Summary

Foreign Exchange (FX) markets are well known to be one of the most complicated markets to both trading and investment. It is not only because the concept of "currency pair" where with merely 8 currencies they can derive 28 unique pairs, but also the unique macro themes that impact each currency. The macro themes that have been explored in this project include global economy growth, yield curve inversion and commodities growth. These factors are definitely not conclusive, as they will be other macro themes that are not able to capture in this project. Which might support the above notion on FX being one of the most complicated markets. 

This project attempts to group the 28 unique FX pairs using an unsupervised machine learning method called clustering. Various clustering method has been attempted, such as KMeans and Hierarchical Clustering. We then leverage on my domain expertise to determine the best clustering method. I then conducted a Principal Component Analysis (PCA) on each cluster, leveraging the first two principal components (PC) to identify the potential major contributing factor of the price movement in these clusters. Additionally, capture ratio by each cluster on the respective macro factor has also been explored. The ratio could indicate how much of the total return of the cluster is expected for an one unit move in the respective macro theme. These could provide insights for portfolio manager in portfolio hedging, risk managements for traders or general knowledge to any aspiring FX traders.

## Problem Statement

I hope to provide insights on and attempt to simplify FX market. I aim to achieve that by identifying similar FX pairs and the respective contributing macro market themes. Instead of treating each individual currency pair uniquely (although they technically still are), I attempt to treat them as a group that share the same overarching macro narrativces. 

## Organisation of the codebook
1.  Data Cleaning
2.  Exploretary Data Analysis
3.  Cluster
4.  PCA
5.  Conclusion

## The Workflow

### The data

Currency pair data are extracted from Reuters using its in-house Python script. Currencies included are AUD, NZD, EUR, GBP, CHF, JPY, CAD and USD. They would then have the following 28 unique pairs: 

       'USDAUD', 'USDNZD', 'USDEUR', 'USDGBP', 'USDCAD', 'USDCHF', 'USDJPY',
       'EURAUD', 'GBPAUD', 'AUDCAD', 'AUDCHF', 'AUDJPY', 'EURNZD', 'CHFJPY',
       'GBPNZD', 'NZDCAD', 'NZDCHF', 'NZDJPY', 'EURGBP', 'EURCAD', 'AUDNZD', 
       'EURCHF', 'EURJPY', 'GBPCAD', 'GBPCHF', 'GBPJPY', 'CADCHF','CADJPY',
       
Due to market convention, some of the pairs like AUDUSD and GBPUSD have been converted to USDAUD and USDGBP during the data cleaning process. Weekly data are extracted from 1996 to 2023. Weekly data are extracted to avoid the noise from daily data and to reduce the price volatility. However, during the EDA process, I decided the project should stick with 2002 to 2023 as 1996 was the beginning of 1997 Asia Financial Crisis, and 2002 is the begining of the new cycle. 

Macro factors are extracted from both Reuters in-house Python function and Bloomberg via Bloomberg API on excel. The Bloomberg tickers and the respective explanation are as per below:

       MXWO Index: MSCI Growth Index
       RMZ Index: MSCI US REITS index
       SPX Index: S&P 500
       BCOM Index: Bloomberg Commodities Index
       
The Reuters tickers and the respective explanation are as per below:

       US2YT=RR: The yield of the 2-year US Treasury bonds
       US10YT=RR: The yield of the 10-year US Treasury bonds
       diff: The difference between the above two (10y minus 2y)
       

All data in consideration for this project are weekly data, from 2002 to 2023. Average Weekly return has been explored for the purpose of KMeans clustering. Correlation matrix on both price and weekly return are also explored for hierarchical clustering.  

### Clustering

Clustering is one of the unsupervised machine learning methods. One of the main purposes of unsupervised machine learning is to group similar data points, and clustering, as the name suggests, would achieve just that. By leveraging on clustering, I attempted to group the 28 pairs of currencies based on different similarity measurement and different clustering algorithms. 

I first attempted to group them by the metrix "average weekly return" across 2002 to 2023. This effectively removed the dimension of time, and use a single metrix to represent the currency pair. KMeans clustering algorithm is used in this case as each currency can now be represented as one data point. KMeans utilises distance between and amongst the entire data, and after transforming each currency into a point, KMeans seems appropriate. Elbow curve and silhouette score are incorporated to determine the best number of cluster. Industrial experience and domain knowledge are utilised here to determine the validity of the resulted clusters. It can be concluded that this is an overly naive approach as it assumes a single matrix could encompass the very nature of each currency. Furtherore, Each currency is a time series and it needs to be taken into consideration. Evidently this method did not lead to a decent set of result. 

I then attempted to group the currencies by correlation matrix. Two matrixes were in consideration, namely correlation on price, and correlation on weekly average return. Hierarchical clustering algorithm is used in this case since we are dealing with matrixes. A threshold has been set intuitively to achieve the appropriate split in clusters. Both sets of clusters (cluster from price correlation and average return correlation) offer much more explanable splits of the data compared to the KMeans clusters. This is most likely because the matrixes used in hierarchical clustering are able to capture the "behaviour" of the currency relative to the other currency. This is however, assuming that the currency pairs with relatively high degree of correlation has high similarity. Intuitively it makes sense, but this method still does not take the nature of time series into consideration. 

I finally attempted to group the currencies through a similarity matrix derived from Dynamic Time Warping (DTW). DTW is an algorithm used in time series analysis, where it measures the similarity between two temporal sequences. More details can be found in this link "https://rtavenar.github.io/blog/dtw.html". Hierarchical clustering is then applied to the resulted similarity metrix from the DTW algorithm. The resulted clusters look to have the most distinctive separation compared to all the previous attempts. These clusters from DTW metrix match my domain understanding the best, while they also offer some insights into the currency. For instance, EURGBP is in the same cluster as CHFJPY. The former is normally driven by risk sentiment in the UK relative to the Eurozone, while the latter is the relative preference between two safe heaven currencies in different regions. A weakening of GBP would lead to higher EURGBP representing risk off, which that evidently accompanies by a preference of the Eurozone safe heaven CHF over JPY, thus a higher CHFJPY.

### PCA

Principal Component Analysis (PCA) is normally used to reduce the dimensionality of the data. A helper function is constructed to execute PCA on the cluster and chart the first two principal components (PCs). Since the first two PCs could normally explain majority of the variance in the set of data, these charts are then used to identify and match other time series data in the market. These other time series are mostly a measurement of the particular themes in the macro markets. This is in hope that the identified time series, or macro theme, could potentially "explain" the variance in the cluster. PCA and charting have been done on all clusters from the above. 

Since I have determined that the clusters from DTW matrix seem to be the best, I attempted and managed to identify a macro theme that seems to have a good fit to the PCs in each respective DTW clusters. They are MSCI Growth Index, US Treasury Yield Differential (2y vs 10y) and Bloomberg Commodities Index. It was then identified that the total return in each cluster have about 50% chance that matches the same direction as the respective macro theme. This means, if the macro theme went up, there is 50% chance that the respective cluster in total also goes up. Capture ratio is also explored to identify feasibility and effectiveness of investment portfolio hedging using the FX cluster. 

### Drawback

#### Incomplete risk capture
Foreign Exchange market consists of multiple agents at play, and they each have their unique agendas, constraints and target timeline. These agents include but not limited to real money, institutions, speculators, governments and more. Market regimes are subjected to change, where a particular macro theme can be of heavy influence in certain market regime than the other. Not to mention the geopolitical elements and any blackswan events such as the recent COVID 19 pandemic or terrorism attack like 9-11 event. All the currencies respectively would have their distinctive idiosyncratic risk exposure to the elements mentioned above, which will not be simply summarised by a PC line. 

#### Clustering methodologies
Other limitation would include the nature of clustering algorithm, where it lacks interpretability on the cluster. Without sufficient domain knowledge, it may be difficult for one to verify the correctness of the cluster. Even for experienced market practitioner, like me, who could not justify the validity of the cluster without looking deeper into the potential connection. Furthermore, number of clusters is determined solely on my discretion. For Kmeans is the predefined number of cluster, while for hierarchical clustering is threshold. The resulted clusters could be drastically different based on the parameter selected. 

## Conclusion and improvements to consider

This is a different approach in dissecting the FX market and I enjoyed utilising different clustering algorithms for such exploration. I will not vouch that this project can provide some "ground truths" about FX, but I hope it shed some lights on some of the driving factors in the FX market. To some extent, I hope it demythifies the FX markets, but it still does not change the fact that FX is one of the most complicated markets to study and make trading profit out of. Nevertheless, this was a great learning experience and I look forward to exploring more projects like this to identify insights that potentially tradable ideas. 

#### Improvements to consider
1. Market-regime-based clustering analysis might be able to apply here. This would offer some degree of predictability to the modeling, where we might be able to determine certain currency pair movement based on the cluster that it should be in during the specific market regime. This however requires the identification of a market regime, which is not an easily quantifiable thing. (Though a lot of places are trying to)

2. Different matrixes can be explored. Distance matric such as euclidean distance in the time series, or similarity metric such as Kendall or Spearman correlations which measure the monotonic relationship of how likely it is for two variables to move in the same direction can be explored further. 

3. More currency pairs can be included. So far we have only considered 8 currencies 28 unique pairs. Adding more currencies especially those in the Emerging market might provide even more meaningful insights. I will be interested to know which currency pairs in the developed country could be traded relatively similar to the one in Emerging markets. 
