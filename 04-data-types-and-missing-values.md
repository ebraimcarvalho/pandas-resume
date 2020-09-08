#### Dtypes

The data type for a column in a DataFrame or a Series is known as the dtype.

You can use the dtype property to grab the type of a specific column. For instance, we can get the dtype of the price column in the reviews DataFrame:

``` python
reviews.price.dtype
#dtype('float64')
```

Alternatively, the dtypes property returns the dtype of every column in the DataFrame:

``` python
reviews.dtypes
#country        object
#description    object
#                ...  
#variety        object
#winery         object
#Length: 13, dtype: object
```

Data types tell us something about how pandas is storing the data internally. float64 means that it's using a 64-bit floating point number; int64 means a similarly sized integer instead, and so on.

One peculiarity to keep in mind (and on display very clearly here) is that columns consisting entirely of strings do not get their own type; they are instead given the object type.

It's possible to convert a column of one type into another wherever such a conversion makes sense by using the astype() function. For example, we may transform the points column from its existing int64 data type into a float64 data type:

``` python
reviews.points.astype('float64')
#0         87.0
#1         87.0
#          ... 
#129969    90.0
#129970    90.0
#Name: points, Length: 129971, dtype: float64
```

A DataFrame or Series index has its own dtype, too:

``` python
reviews.index.dtype
#dtype('int64')
```

Pandas also supports more exotic data types, such as categorical data and timeseries data. Because these data types are more rarely used, we will omit them until a much later section of this tutorial.

#### Missing data

Entries missing values are given the value NaN, short for "Not a Number". For technical reasons these NaN values are always of the float64 dtype.

Pandas provides some methods specific to missing data. To select NaN entries you can use pd.isnull() (or its companion pd.notnull()). This is meant to be used thusly:

``` python
reviews[pd.isnull(reviews.country)]
# get all rows with country == NaN
```

Replacing missing values is a common operation. Pandas provides a really handy method for this problem: fillna(). fillna() provides a few different strategies for mitigating such data. For example, we can simply replace each NaN with an "Unknown":

``` python
reviews.region_2.fillna("Unknown")
# replace all NaN in region_2 with 'Unknown'
```

Or we could fill each missing value with the first non-null value that appears sometime after the given record in the database. This is known as the backfill strategy.

Alternatively, we may have a non-null value that we would like to replace. For example, suppose that since this dataset was published, reviewer Kerin O'Keefe has changed her Twitter handle from @kerinokeefe to @kerino. One way to reflect this in the dataset is using the replace() method:

``` python
reviews.taster_twitter_handle.replace("@kerinokeefe", "@kerino")
```

##### Exercises

1. What is the data type of the points column in the dataset?

``` python
dtype = reviews.points.dtype
```

2. Create a Series from entries in the points column, but convert the entries to strings. Hint: strings are str in native Python.

``` python
point_strings = reviews.points.astype('str')
```

3. Sometimes the price column is null. How many reviews in the dataset are missing a price?

``` python
n_missing_prices = reviews.price.isnull().sum()
# or
missing_price_reviews = reviews[reviews.price.isnull()]
n_missing_prices = len(missing_price_reviews)
# or
n_missing_prices = pd.isnull(reviews.price).sum()
```