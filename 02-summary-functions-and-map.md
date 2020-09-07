#### Summary functions

Pandas provides many simple "summary functions" (not an official name) which restructure the data in some useful way. For example, consider the describe() method:

``` python
reviews.points.describe()
#count    129971.000000
#mean         88.447138
#             ...      
#75%          91.000000
#max         100.000000
#Name: points, Length: 8, dtype: float64
```

This method generates a high-level summary of the attributes of the given column. It is type-aware, meaning that its output changes based on the data type of the input. The output above only makes sense for numerical data; for string data here's what we get:

``` python
reviews.taster_name.describe()
#count         103727
#unique            19
#top       Roger Voss
#freq           25514
#Name: taster_name, dtype: object
```