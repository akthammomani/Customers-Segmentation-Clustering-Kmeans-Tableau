# Project: Optimizing Wholesale Wine business by segmenting customers behavior using Machine Learning - Kmeans powered by Tableau

<p align="center">
  <img width="1000" height="400" src="https://user-images.githubusercontent.com/67468718/108179916-03ce9e80-70bb-11eb-937d-702f5c5151d9.JPG">
</p>

## 1. Introduction

This project is based on a book called [Data Smart](https://www.amazon.com/Data-Smart-Science-Transform-Information/dp/111866146X), written by John Foreman, head of product for MailChimp. This book is an excellent business analytics primer that walks you through a variety of machine learning use cases, complete with sample data sets and detailed instructions on how to set up and solve each case in Excel. 

One of these cases, John describes a fictitious wholesale wine business looking to optimize sales by segmenting customers — restaurant and liquor store owners, into groups based on buying behavior. This is a very common problem faced by all kinds of businesses from large retailers like Walmart and Target to your friendly neighborhood liquor store.

## 2. Dataset

The dataset contains information on marketing newsletters/e-mail campaigns (e-mail offers sent to customers) and transaction level data from customers. The transactional data shows which offer customers responded to, and what the customer ended up buying. The data is presented as an Excel workbook containing two worksheets. Each worksheet contains a different dataset.

We see that the first dataset <code>**WineKMC.xlsx sheet 0**</code>  contains information about each offer such as the month it is in effect and several attributes about the wine that the offer refers to: the variety, minimum quantity, discount, country of origin and whether or not it is past peak. 

The second dataset <code>**WineKMC.xlsx sheet 1**</code> in the second worksheet contains transactional data -- which offer each customer responded to.

## 3. Data Wrangling

We're trying to learn more about how our customers behave, so we can use their behavior (whether or not they purchased something based on an offer) as a way to group similar minded customers together. We can then study those groups to look for patterns and trends which can help us formulate future offers.

The first thing we need is a way to compare customers. To do this, we're going to create a matrix that contains each customer and a 0/1 indicator for whether or not they responded to a given offer.

  * Merge <code>**df_transactions**</code> & <code>**df_offers**</code> on=<code>**offer_id**</code> 
  * Make a pivot table, to show a matrix with customer names in the row index and offer numbers as columns headers and fill NANs with zeros.

## 4. K-Means Clustering (Choosing K)

Recall that in K-Means Clustering we want to maximize the distance between centroids and minimize the distance between data points and the respective centroid for the cluster they are in. True evaluation for unsupervised learning would require labeled data; however, we can use a variety of intuitive metrics to try to pick the number of clusters K. We will introduce two methods: the Elbow method, the Silhouette method and the gap statistic.

  * Choosing  𝐾 : The Elbow Method:
    * **Distortion:** It is calculated as the average of the squared distances from the cluster centers of the respective clusters. Typically, **the Euclidean distance metric is used.**
    * **Inertia:** It is the sum of squared distances of samples to their closest cluster center. Typically, **inertia_ attribute from kmeans is used.
    * Lastly, we look at the sum-of-squares error in each cluster against $K$. We compute the distance from each data point to the center of the cluster (centroid) to which the data point was assigned. 

  * Choosing  𝐾 : The Silhouette Method
    
    There exists another method that measures how well each datapoint  𝑥𝑖  "fits" its assigned cluster and also how poorly it fits into other clusters. This is a different way of looking at the same objective. Denote  𝑎𝑥𝑖  as the average distance from  𝑥𝑖  to all other points within its own cluster  𝑘 . The lower the value, the better. On the other hand  𝑏𝑥𝑖  is the minimum average distance from  𝑥𝑖  to points in a different cluster, minimized over clusters. That is, compute separately for each cluster the average distance from  𝑥𝑖  to the points within that cluster, and then take the minimum.
    
    The silhouette score is computed on every datapoint in every cluster. The silhouette score ranges from -1 (a poor clustering) to +1 (a very dense clustering) with 0 denoting the situation where clusters overlap. Some criteria for the silhouette coefficient is provided in the table below:
    
| Range       | Interpretation                                |
|:-------------|:-----------------------------------------------:|
| 0.71 - 1.0  | A strong structure has been found.            |
| 0.51 - 0.7  | A reasonable structure has been found.        |
| 0.26 - 0.5  | The structure is weak and could be artificial.|
| < 0.25      | No substantial structure has been found.      |

[Source](http://www.stat.berkeley.edu/~spector/s133/Clus.html)

<p align="center">
  <img width="800" height="400" src="https://user-images.githubusercontent.com/67468718/108182880-59587a80-70be-11eb-8b28-222a1f0ad928.JPG">
</p>

<p align="center">
  <img width="800" height="400" src="https://user-images.githubusercontent.com/67468718/108182885-59f11100-70be-11eb-95e0-f302c17df95d.JPG">
</p>

<p align="center">
  <img width="800" height="400" src="https://user-images.githubusercontent.com/67468718/108182886-5a89a780-70be-11eb-9a92-875d1de81849.JPG">
</p>


![silhouette](https://user-images.githubusercontent.com/67468718/108188526-8871ea80-70c4-11eb-9f63-d2dcbca2974f.JPG)

<p align="center">
  <img width="800" height="400" src="https://user-images.githubusercontent.com/67468718/108189239-6036bb80-70c5-11eb-9691-bb35849148e3.JPG">
</p>


## 5. Choosing  𝐾 Summary:
  * **Elbow Method using Distortion from Scipy** confirms that the <code>**elbow point k=3**</code> so the <code>**best k will be=4**</code> (plot starts descending much more slowly after k=3)
  * **Elbow Method using Inertia from kmeans** confirms that the <code>**elbow point k=3**</code> so the <code>**best k will be=4**</code> (plot starts descending much more slowly after k=3)
  * Unfortunately, **Elbow Method using Sum of Squares - SSE** , this plot doesn’t show a real clear “elbow point”, which means it is not straightforward to find the number of clusters using this Sum-of-Squares (SSE) Method. however, from above we can see that <code>k=3 is our most obvious elbow point</code>, Thus <code>best option for K will be 4</code> **(plot starts descending much more slowly after k=3).**
  * Again, The Silhouette Method, there does not appear to be a clear way to find best K in this method, which means it is not straightforward to find the number of clusters using this method. however, from above we can see <code>k=10, 3 and 9 </code>are our best option for now **(Closest to 1 = best Silhouette Score, but clearly Silhouette Scores are relatively small suggesting we have a weak structure!!!)**


## 6. Visualizing Clusters using PCA

How do we visualize clusters? If we only had two features, we could likely plot the data as is. But we have 100 data points each containing 32 features (dimensions). Principal Component Analysis (PCA) will help us reduce the dimensionality of our data from 32 to something lower. For a visualization on the coordinate plane, we will use 2 dimensions. In this exercise, we're going to use it to transform our multi-dimensional dataset into a 2 dimensional dataset.

This is only one use of PCA for dimension reduction. We can also use PCA when we want to perform regression but we have a set of highly correlated variables. PCA untangles these correlations into a smaller number of features/predictors all of which are orthogonal (not correlated). PCA is also used to reduce a large set of variables into a much smaller one.

<p align="center">
  <img width="800" height="400" src="https://user-images.githubusercontent.com/67468718/108194935-0be30a00-70cc-11eb-8ca1-e494895deeae.JPG">
</p>

    








