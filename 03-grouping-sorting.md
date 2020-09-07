#### Introduction

Maps allow us to transform data in a DataFrame or Series one value at a time for an entire column. However, often we want to group our data, and then do something specific to the group the data is in.

As you'll learn, we do this with the groupby() operation. We'll also cover some additional topics, such as more complex ways to index your DataFrames, along with how to sort your data.


###### Groupwise analysis

One function we've been using heavily thus far is the value_counts() function. We can replicate what value_counts() does by doing the following:

``` python
reviews.groupby('points').points.count()
points
#80     397
#81     692
#      ... 
#99      33
#100     19
#Name: points, Length: 21, dtype: int64
```

groupby() created a group of reviews which allotted the same point values to the given wines. Then, for each of these groups, we grabbed the points() column and counted how many times it appeared. value_counts() is just a shortcut to this groupby() operation.

We can use any of the summary functions we've used before with this data. For example, to get the cheapest wine in each point value category, we can do the following:

``` python
reviews.groupby('points').price.min()
points
#80      5.0
#81      5.0
#       ... 
#99     44.0
#100    80.0
#Name: price, Length: 21, dtype: float64
```

You can think of each group we generate as being a slice of our DataFrame containing only data with values that match. This DataFrame is accessible to us directly using the apply() method, and we can then manipulate the data in any way we see fit. For example, here's one way of selecting the name of the first wine reviewed from each winery in the dataset:

``` python
reviews.groupby('winery').apply(lambda df: df.title.iloc[0])
winery
#1+1=3                          1+1=3 NV Rosé Sparkling (Cava)
#10 Knots                 10 Knots 2010 Viognier (Paso Robles)
#                                  ...                        
#àMaurice    àMaurice 2013 Fred Estate Syrah (Walla Walla V...
#Štoka                         Štoka 2009 Izbrani Teran (Kras)
#Length: 16757, dtype: object
```

For even more fine-grained control, you can also group by more than one column. For an example, here's how we would pick out the best wine by country and province:

``` python
reviews.groupby(['country', 'province']).apply(lambda df: df.loc[df.points.idxmax()])
```

Another groupby() method worth mentioning is agg(), which lets you run a bunch of different functions on your DataFrame simultaneously. For example, we can generate a simple statistical summary of the dataset as follows:

``` python
reviews.groupby(['country']).price.agg([len, min, max])
#           len	    min	 max
#country			
#Argentina	3800.0	4.0	 230.0
#Armenia	  2.0	    14.0 15.0
#...	...	...	...
#Ukraine	  14.0	  6.0	 13.0
#Uruguay	  109.0	  10.0 130.0
```