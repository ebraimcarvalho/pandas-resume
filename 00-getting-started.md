### Getting Started with python pandas

This is some few summary and notes of my learning process with pandas

``` python
# To import pandas library
import pandas as pd

# To create the first Dataframe with intengers
pd.DataFrame({'Yes': [50, 21], 'No': [131, 2]})

#    Yes  No
# 0  50	  131
# 1  21	  2

# We also can create a dataframe with strings
pd.DataFrame({'Bob': ['I liked it.', 'It was awful.'], 
							'Sue': ['Pretty good.', 'Bland.']})
#   Bob	          Sue
#0 I liked it.	  Pretty good.
#1 It was awful.  Bland.

# We can set a name to our index row
pd.DataFrame({'Bob': ['I liked it.', 'It was awful.'], 
              'Sue': ['Pretty good.', 'Bland.']},
             index=['Product A', 'Product B'])
#           Bob	          Sue
#Product A  I liked it.	  Pretty good.
#Product B  It was awful. Bland.

# Starting with Series
pd.Series([1, 2, 3, 4, 5])
#0    1
#1    2
#2    3
#3    4
#4    5
#dtype: int64

# We can set a name to our index row too
pd.Series([30, 35, 40], index=['2015 Sales', '2016 Sales', '2017 Sales'], 
					name='Product A')
#2015 Sales    30
#2016 Sales    35
#2017 Sales    40
#Name: Product A, dtype: int64
```

### The Series and the DataFrame are intimately related. It's helpful to think of a DataFrame as actually being just a bunch of Series "glued together".

### Now, let's try read a csv file!

``` python
# We create a variable called df and store a dataframe called with pd.read_csv with the csv file name
df = pd.read_csv('file_to_read.csv')

# df.shape shows to us how many rows and columns we have
df.shape
# (129971, 14) -> 129971 rows and 14 columns

# df.head() shows the first 5 rows of Dataframe
df.head()
```

The pd.read_csv() function is well-endowed, with over 30 optional parameters you can specify. For example, you can see in this dataset that the CSV file has a built-in index, which pandas did not pick up on automatically. 
To make pandas use that column for the index (instead of creating a new one 
from scratch), we can specify an index_col.

``` python
df = pd.read_csv('file_to_read.csv', index_col=0) 
# index_col=0 starts our column at index 0
df.head()

# Save our dataframe in a file csv called my_new_file
df.to_csv('my_new_file.csv') 
```
