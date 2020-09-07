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

##### Multi-indexes

In all of the examples we've seen thus far we've been working with DataFrame or Series objects with a single-label index. groupby() is slightly different in the fact that, depending on the operation we run, it will sometimes result in what is called a multi-index.

A multi-index differs from a regular index in that it has multiple levels. For example:

``` python
countries_reviewed = reviews.groupby(['country', 'province']).description.agg([len])
countries_reviewed
#		                          len
#country	province	
#Argentina	Mendoza Province	3264
#Other	                      536
#...	...	...
#Uruguay	San Jose	          3
#Uruguay	                    24

mi = countries_reviewed.index
type(mi)
#pandas.core.indexes.multi.MultiIndex
```

Multi-indices have several methods for dealing with their tiered structure which are absent for single-level indices. They also require two levels of labels to retrieve a value. Dealing with multi-index output is a common "gotcha" for users new to pandas.

The use cases for a multi-index are detailed alongside instructions on using them in the MultiIndex / Advanced Selection section of the pandas documentation.

However, in general the multi-index method you will use most often is the one for converting back to a regular index, the reset_index() method:

``` python
countries_reviewed.reset_index()
#   country	  province	        len
#0	Argentina	Mendoza Province	3264
#1	Argentina	Other             536
#...	...	...	...
#423	Uruguay	San Jose	        3
#424	Uruguay	Uruguay	          24
```

#### Sorting

Looking again at countries_reviewed we can see that grouping returns data in index order, not in value order. That is to say, when outputting the result of a groupby, the order of the rows is dependent on the values in the index, not in the data.

To get data in the order want it in we can sort it ourselves. The sort_values() method is handy for this.

``` python
countries_reviewed = countries_reviewed.reset_index()
countries_reviewed.sort_values(by='len')
#     country	province	            len
#179	Greece	Muscat of Kefallonian	1
#192	Greece	Sterea Ellada	        1
#...	...	...	...
#415	US	    Washington	          8639
#392	US	    California	          36247
```

sort_values() defaults to an ascending sort, where the lowest values go first. However, most of the time we want a descending sort, where the higher numbers go first. That goes thusly:

``` python
countries_reviewed.sort_values(by='len', ascending=False)
#     country	province	  len
#392	US	    California	36247
#415	US	    Washington	8639
#...	...	...	...
#63	  Chile	  Coelemu	    1
#149	Greece	Beotia	    1
```

To sort by index values, use the companion method sort_index(). This method has the same arguments and default order:

``` python
countries_reviewed.sort_index()
#     country	  province	        len
#0	  Argentina	Mendoza Province	3264
#1	  Argentina	Other	            536
#...	...	...	...
#423	Uruguay	  San Jose	        3
#424	Uruguay	  Uruguay	          24
```

Finally, know that you can sort by more than one column at a time:

``` python
countries_reviewed.sort_values(by=['country', 'len'])
#     country	  province	        len
#1	  Argentina	Other	            536
#0	  Argentina	Mendoza Province	3264
#...	...	...	...
#424	Uruguay	  Uruguay	          24
#419	Uruguay	  Canelones	        43
```