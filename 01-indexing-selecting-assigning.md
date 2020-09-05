### Native accessors

Native Python objects provide good ways of indexing data. Pandas carries all of these over, which helps make it easy to start with.

In Python, we can access the property of an object by accessing it as an attribute. A book object, for example, might have a title property, which we can access by calling book.title. Columns in a pandas DataFrame work in much the same way.

Hence to access the country property of reviews we can use:

``` python
reviews.country
#0            Italy
#1         Portugal
#            ...   
#129969      France
#129970      France
#Name: country, Length: 129971, dtype: object

```

If we have a Python dictionary, we can access its values using the indexing ([]) operator. We can do the same with columns in a DataFrame:

``` python
reviews['country']
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
reviews['country'][0]
'Italy'
```