### Native accessors

Native Python objects provide good ways of indexing data. Pandas carries all of these over, which helps make it easy to start with.

In Python, we can access the property of an object by accessing it as an attribute. A book object, for example, might have a title property, which we can access by calling book.title. Columns in a pandas DataFrame work in much the same way.

Hence to access the country property of reviews we can use:

``` python
reviews.country # get all rows of the column name country
#0            Italy
#1         Portugal
#            ...   
#129969      France
#129970      France
#Name: country, Length: 129971, dtype: object

```

If we have a Python dictionary, we can access its values using the indexing ([]) operator. We can do the same with columns in a DataFrame:

``` python
reviews['country'] # get all rows of the column name country
#0            Italy
#1         Portugal
#            ...   
#129969      France
#129970      France
#Name: country, Length: 129971, dtype: object
```

But if we had a country providence column, reviews.country providence wouldn't work).

Doesn't a pandas Series look kind of like a fancy dictionary? It pretty much is, so it's no surprise that, to drill down to a single specific value, we need only use the indexing operator [] once more:

``` python
reviews['country'][0] # get the row index 0 of column name country
#'Italy'
```

Pandas has its own accessor operators, loc and iloc. For more advanced operations, these are the ones you're supposed to be using.

#### Index-based selection
Pandas indexing works in one of two paradigms. The first is index-based selection: selecting data based on its numerical position in the data. iloc follows this paradigm.

To select the first row of data in a DataFrame, we may use the following:
``` python
reviews.iloc[0] # get all row index 0
#country                                                    Italy
#description    Aromas include tropical fruit, broom, brimston...
#                                     ...                        
#variety                                              White Blend
#winery                                                   Nicosia
#Name: 0, Length: 13, dtype: object
```

Both 'loc' and 'iloc' are row-first, column-second. This is the opposite of what we do in native Python, which is column-first, row-second.

This means that it's marginally easier to retrieve rows, and marginally harder to get retrieve columns. To get a column with iloc, we can do the following:

``` python
reviews.iloc[:, 0] # get the column index 0 of all rows
#0            Italy
#1         Portugal
#            ...   
#129969      France
#129970      France
#Name: country, Length: 129971, dtype: object
```

On its own, the : operator, which also comes from native Python, means "everything". When combined with other selectors, however, it can be used to indicate a range of values. For example, to select the country column from just the first, second, and third row, we would do:

``` python
reviews.iloc[:3, 0] # get the rows 0, 1 and 2, give me the column 0
#0       Italy
#1    Portugal
#2          US
#Name: country, dtype: object
```

Or, to select just the second and third entries, we would do:

``` python
reviews.iloc[1:3, 0] # get the rows 1 and 2, give me the column 0
#1    Portugal
#2          US
#Name: country, dtype: object
```

It's also possible to pass a list:

``` python
reviews.iloc[[0, 1, 2], 0] # get the rows 0, 1 and 2, give me the column 0
#0       Italy
#1    Portugal
#2          US
#Name: country, dtype: object
```

Finally, it's worth knowing that negative numbers can be used in selection. This will start counting forwards from the end of the values. So for example here are the last five elements of the dataset.

``` python
reviews.iloc[-5:] # get the last 5 rows, give me all columns
```

#### Label-based selection
The second paradigm for attribute selection is the one followed by the loc operator: label-based selection. In this paradigm, it's the data index value, not its position, which matters.

For example, to get the first entry in reviews, we would now do the following:

``` python
reviews.loc[0, 'country'] # get the row index 0, column label country
#'Italy'
```

iloc is conceptually simpler than loc because it ignores the dataset's indices. When we use iloc we treat the dataset like a big matrix (a list of lists), one that we have to index into by position. loc, by contrast, uses the information in the indices to do its work. Since your dataset usually has meaningful indices, it's usually easier to do things using loc instead. For example, here's one operation that's much easier using loc:

``` python
reviews.loc[:, ['taster_name', 'taster_twitter_handle', 'points']] # get all rows, give just 
# the columns label taster_name, taster_twitter_handle and points

#   taster_name	    taster_twitter_handle	points
#0	Kerin O’Keefe	  @kerinokeefe	        87
#1	Roger Voss	    @vossroger	          87
#.. ...	            ...	                  ...
#129969	Roger Voss	@vossroger	          90
#129970	Roger Voss	@vossroger	          90
```

#### Choosing between loc and iloc
When choosing or transitioning between loc and iloc, there is one "gotcha" worth keeping in mind, which is that the two methods use slightly different indexing schemes.

iloc uses the Python stdlib indexing scheme, where the first element of the range is included and the last one excluded. So 0:10 will select entries 0,...,9. loc, meanwhile, indexes inclusively. So 0:10 will select entries 0,...,10.

Why the change? Remember that loc can index any stdlib type: strings, for example. If we have a DataFrame with index values Apples, ..., Potatoes, ..., and we want to select "all the alphabetical fruit choices between Apples and Potatoes", then it's a lot more convenient to index df.loc['Apples':'Potatoes'] than it is to index something like df.loc['Apples', 'Potatoet] (t coming after s in the alphabet).

This is particularly confusing when the DataFrame index is a simple numerical list, e.g. 0,...,1000. In this case df.iloc[0:1000] will return 1000 entries, while df.loc[0:1000] return 1001 of them! To get 1000 elements using loc, you will need to go one lower and ask for df.loc[0:999].

Otherwise, the semantics of using loc are the same as those for iloc.

#### Manipulating the index
Label-based selection derives its power from the labels in the index. Critically, the index we use is not immutable. We can manipulate the index in any way we see fit.

The set_index() method can be used to do the job. Here is what happens when we set_index to the title field:

``` python
reviews.set_index("title")
```

Conditional selection
So far we've been indexing various strides of data, using structural properties of the DataFrame itself. To do interesting things with the data, however, we often need to ask questions based on conditions.

For example, suppose that we're interested specifically in better-than-average wines produced in Italy.

We can start by checking if each wine is Italian or not:

``` python
reviews.country == 'Italy' # show all records with true or false if country == Italy
#0          True
#1         False
#          ...  
#129969    False
#129970    False
Name: country, Length: 129971, dtype: bool
```

This operation produced a Series of True/False booleans based on the country of each record. This result can then be used inside of loc to select the relevant data:

``` python
reviews.loc[reviews.country == 'Italy'] # get all rows with country == Italy
```

This DataFrame has ~20,000 rows. The original had ~130,000. That means that around 15% of wines originate from Italy.

We also wanted to know which ones are better than average. Wines are reviewed on a 80-to-100 point scale, so this could mean wines that accrued at least 90 points.

We can use the ampersand (&) to bring the two questions together:

``` python
reviews.loc[(reviews.country == 'Italy') & (reviews.points >= 90)]
# get all rows with country == Italy and points >= 90
```

Suppose we'll buy any wine that's made in Italy or which is rated above average. For this we use a pipe (|):

``` python
reviews.loc[(reviews.country == 'Italy') | (reviews.points >= 90)]
# get all rows with contry == Italy OR points >= 90
```

Pandas comes with a few built-in conditional selectors, two of which we will highlight here.

The first is isin. isin is lets you select data whose value "is in" a list of values. For example, here's how we can use it to select wines only from Italy or France:

``` python
reviews.loc[reviews.country.isin(['Italy', 'France'])]
# Get all rows with conutry == Italy Or France
```

The second is isnull (and its companion notnull). These methods let you highlight values which are (or are not) empty (NaN). For example, to filter out wines lacking a price tag in the dataset, here's what we would do:
``` python
reviews.loc[reviews.price.notnull()]
# Get all rows with Price not Null
```

#### Assigning data
Going the other way, assigning data to a DataFrame is easy. You can assign either a constant value:

``` python
reviews['critic'] = 'everyone'
reviews['critic']
#0         everyone
#1         everyone
#            ...   
#129969    everyone
#129970    everyone
#Name: critic, Length: 129971, dtype: object

#Or with an iterable of values:

reviews['index_backwards'] = range(len(reviews), 0, -1)
reviews['index_backwards']
#0         129971
#1         129970
#           ...  
#129969         2
#129970         1
#Name: index_backwards, Length: 129971, dtype: int64
```

``` python
df = pd.DataFrame({'Yes': [50, 21], 'No not': [131, 2]})
print(df['Yes'][1]) #column and row
# 21
print(df.iloc[1,0]) # row and column by index
# 21
print(df.loc[1, 'Yes']) # row and column by label (name column)
# 21

first_description = reviews.description.iloc[0]
first_description = reviews.iloc[0, 1]
#Note that while this is the preferred way to obtain the entry in the DataFrame, many other options will return a valid result, such as 
reviews.description.loc[0]
reviews.description[0] # and more!
```

###### Select the first 10 values from the description column in reviews, assigning the result to variable first_descriptions.

Hint: format your output as a pandas Series.

first_descriptions = reviews.description[:10]
first_descriptions = reviews.description[:10]
first_descriptions = reviews.description.iloc[:10]
Note that many other options will return a valid result, such as desc.head(10) and reviews.loc[:9, "description"].

###### Select the records with index labels 1, 2, 3, 5, and 8, assigning the result to the variable sample_reviews.
sample_reviews = reviews.iloc[[1,2,3,5,8]]
indices = [1, 2, 3, 5, 8]
sample_reviews = reviews.loc[indices]

###### Create a variable df containing the country, province, region_1, and region_2 columns of the records with the index labels 0, 1, 10, and 100. In other words, generate the following DataFrame:


df = reviews.loc[[0,1,10,100], ['country', 'province', 'region_1', 'region_2']]
cols = ['country', 'province', 'region_1', 'region_2']
indices = [0, 1, 10, 100]
df = reviews.loc[indices, cols]

###### Create a variable df containing the country and variety columns of the first 100 records.

Hint: you may use loc or iloc. When working on the answer this question and the several of the ones that follow, keep the following "gotcha" described in the tutorial:

iloc uses the Python stdlib indexing scheme, where the first element of the range is included and the last one excluded. loc, meanwhile, indexes inclusively.

This is particularly confusing when the DataFrame index is a simple numerical list, e.g. 0,...,1000. In this case df.iloc[0:1000] will return 1000 entries, while df.loc[0:1000] return 1001 of them! To get 1000 elements using loc, you will need to go one lower and ask for df.iloc[0:999].

df = reviews.loc[0:99, ['country', 'variety']]
cols = ['country', 'variety']
df = reviews.loc[:99, cols]
or

cols_idx = [0, 11]
df = reviews.iloc[:100, cols_idx]


###### Create a DataFrame italian_wines containing reviews of wines made in Italy. Hint: reviews.country equals what?

italian_wines = reviews[reviews['country'] == 'Italy']
italian_wines = reviews[reviews.country == 'Italy']

###### Create a DataFrame top_oceania_wines containing all reviews with at least 95 points (out of 100) for wines from Australia or New Zealand.

top_oceania_wines = reviews[(reviews['points'] >= 95) & (reviews.country.isin(['Australia', 'New Zealand']))]

top_oceania_wines = reviews.loc[
  (reviews.country.isin(['Australia', 'New Zealand']))
  & (reviews.points >= 95)
]