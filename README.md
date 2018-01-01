
Dform
======

# 1. About

- Injecting sql method to Dataframe
- tested with pandas==0.22.0, python==3.6.3

# 2. Installation

```python
pip install -U dform
```

# 3. Implement


```python
import pandas as pd
import numpy as np
from dform import inject_all, inject_selective

inject_all(pd.DataFrame) # inject all orm methods to DataFrame
# inject_selective(pd.DataFrame, 'select', 'map', 'orderby', 'annotate', 'aggregate') # inject selective what you want
```

# 4. API Examples


```python
# sample data sources, simple shopping mall db
userTable = [
    {'name': 'Hong', 'gender': 'M', 'age': 18, 'location': 'Korea', 'point': 72.4},
    {'name': 'Charse', 'gender': 'M', 'age': 19, 'location': 'USA', 'point': 12},
    {'name': 'Lyn', 'gender': 'F', 'age': 28, 'location': 'China', 'point': 0},
    {'name': 'Xiaomi', 'gender': 'M', 'age': 15, 'location': 'China', 'point': 90},
    {'name': 'Park', 'gender': 'M', 'age': 29, 'point': -2.5},
    {'name': 'Smith', 'gender': 'M', 'age': 17, 'location': 'USA', 'point': 10},
    {'name': 'Lee', 'gender': 'F', 'age': 12, 'location': 'Korea','point': 20},
    {'name': 'Michle', 'gender': 'M', 'age': 24, 'location': 'USA','point': 75},
    {'name': 'ChinChin', 'gender': 'M', 'age': 35, 'location': 'China','point': -9},
    {'name': 'Cho', 'gender': 'M', 'age': 29, 'location': 'Korea','point': 2},
    {'name': 'Kim', 'gender': 'F', 'age': 37, 'location': 'Korea','point': 86},
]
productTable = [
    {'product': 'battery', 'price': 100},
    {'product': 'keyboard', 'price': 2000},
    {'product': 'cleaner', 'price': 35},
    {'product': 'monitor', 'price': 7800},
    {'product': 'mouse', 'price': 1500},
    {'product': 'hardcase', 'price': 2300},
    {'product': 'keycover', 'price': 80},
    {'product': 'manual', 'price': 70},
    {'product': 'cable', 'price': 120},
    {'product': 'adopter', 'price': 3000},
]
```

## 4.1. select (overrided)

### 1) general usage


```python
odf = pd.DataFrame(userTable)
```


```python
# retrieve all of dataframe
odf.select('*')
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>72.4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>28</td>
      <td>F</td>
      <td>China</td>
      <td>Lyn</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>M</td>
      <td>China</td>
      <td>Xiaomi</td>
      <td>90.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2.5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>12</td>
      <td>F</td>
      <td>Korea</td>
      <td>Lee</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>86.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# column selecting and filtering by df mask
odf.select('name', 'location', 'point', 
    where=lambda df: (df.age > 20) & (df.gender == 'M') & (df.name.str.contains('Ch'))
)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>location</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8</th>
      <td>ChinChin</td>
      <td>China</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Cho</td>
      <td>Korea</td>
      <td>2.0</td>
    </tr>
  </tbody>
</table>
</div>



### 2) with shortcut filtering expressions


```python
# fix name to $name or name$: generate str contains or regex search mask
odf.select('*',
    where={'gender': 'M', 'age>': 20, 'name$': 'Ch'} # Behavior and as default, options: [&, |, ^]
)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>2.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
odf.select('*',
    where={'gender': 'M', 'age>': 20, 'name$': 'Ch'}, 
    setop = '^' # Behavior and as default, options: [&, |, ^]
)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>72.4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>28</td>
      <td>F</td>
      <td>China</td>
      <td>Lyn</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>M</td>
      <td>China</td>
      <td>Xiaomi</td>
      <td>90.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>86.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
odf.select('*',
    where={'gender': 'M', 'age>': 20, 'name$': 'Ch'},
    setop = '|'
)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>72.4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>28</td>
      <td>F</td>
      <td>China</td>
      <td>Lyn</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>M</td>
      <td>China</td>
      <td>Xiaomi</td>
      <td>90.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2.5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>86.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
odf.select('*', where={'gender': 'M', 'age>': 20, '~name$': 'Ch'},
    setop = '&'
)
# you can exclude row by '~' pre, post fixation on column name like '~name', 'name~', '~age<' 
#gender == M and age > 20 and name not contains 'Ch'
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2.5</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
  </tbody>
</table>
</div>



### 3) complex set operation

##### before complex set op


```python
m1 = ( (odf['age'] > 20) | odf['name'].str.contains('Ch') ) & (odf['gender'] == 'M')
m2 = (odf['age'] > 20) | ( odf['name'].str.contains('Ch') & (odf['gender'] == 'M') )
# two of masks are ret different result(may affected by operating sequence)
```


```python
# Case1:
odf.loc[m1]
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2.5</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>2.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Case2
odf.loc[m2]
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>28</td>
      <td>F</td>
      <td>China</td>
      <td>Lyn</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2.5</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>86.0</td>
    </tr>
  </tbody>
</table>
</div>



####  you can set sequence of operations and mask as you want


```python
odf.select('*', where={'gender': 'M', 'age>': 20, 'name$': 'Ch'}, 
    setop = 'age|name&gender'
)
# (age|name) & gender : Case1 Above
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2.5</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>2.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
odf.select('*', where={'gender': 'M', 'age>': 20, 'name$': 'Ch'}, 
    setop = 'name&gender|age'
)
# (name&gender) | age : Case2 Above
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>28</td>
      <td>F</td>
      <td>China</td>
      <td>Lyn</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2.5</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>86.0</td>
    </tr>
  </tbody>
</table>
</div>



### 4) other usfuls clause

##### - range caluese


```python
odf.select('*', where={'age[]': [15, 25]},
)
# 15 <= age <= 25
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>72.4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>M</td>
      <td>China</td>
      <td>Xiaomi</td>
      <td>90.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# It doesn't matter(same result) list contains 3 more than items: only catches array's min, max
odf.select('*', where={'age[]': [15, 25, 16, 17,20]}, # min is 15, max is 25
)
# 15 <= age <= 25
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>72.4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>M</td>
      <td>China</td>
      <td>Xiaomi</td>
      <td>90.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
odf.select('*', where={'age(]': [15, 25]},
)
# 15 < age <= 25
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>72.4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
odf.select('*', where={'age()': [15, 25]},
)
# 15 < age < 25
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>72.4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
  </tbody>
</table>
</div>



##### - isin clause


```python
odf.select('*', where={'age{}': [15, 12, 37]},
)
# age is in case 12, 15, 37
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>M</td>
      <td>China</td>
      <td>Xiaomi</td>
      <td>90.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>12</td>
      <td>F</td>
      <td>Korea</td>
      <td>Lee</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>86.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# also excludes by '~' : age not in case [12, 15, 37]
odf.select('*', where={'~age{}': [15, 12, 37]},
)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>72.4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>28</td>
      <td>F</td>
      <td>China</td>
      <td>Lyn</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2.5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>2.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
odf.select('*', where={'~gender':'M'})
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>28</td>
      <td>F</td>
      <td>China</td>
      <td>Lyn</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>12</td>
      <td>F</td>
      <td>Korea</td>
      <td>Lee</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>86.0</td>
    </tr>
  </tbody>
</table>
</div>



## 4.2. update (overrided)

### 1) general usage


```python
odf = pd.DataFrame(userTable)
```


```python
# simple single value update
odf.update('point', set=0, where={'gender': 'M', 'age<=': 20})
# gender == M and age <=20 point set to 0
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>28</td>
      <td>F</td>
      <td>China</td>
      <td>Lyn</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>M</td>
      <td>China</td>
      <td>Xiaomi</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2.5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>12</td>
      <td>F</td>
      <td>Korea</td>
      <td>Lee</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>86.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
odf.update('point', set=0, where={'gender': 'M', 'age<=': 20}, setop='|')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>28</td>
      <td>F</td>
      <td>China</td>
      <td>Lyn</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>M</td>
      <td>China</td>
      <td>Xiaomi</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>12</td>
      <td>F</td>
      <td>Korea</td>
      <td>Lee</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>86.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# calculated on df function
odf.update('point', set=lambda df: np.where(df.age > 20, df.point+ 20, df.point - 20))
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>52.4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>-8.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>28</td>
      <td>F</td>
      <td>China</td>
      <td>Lyn</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>M</td>
      <td>China</td>
      <td>Xiaomi</td>
      <td>70.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>17.5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>-10.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>12</td>
      <td>F</td>
      <td>Korea</td>
      <td>Lee</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>95.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>11.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>22.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>106.0</td>
    </tr>
  </tbody>
</table>
</div>



### 4.3 annotate, aggregate(overrided) 


```python
odf = pd.DataFrame(userTable)
```


```python
# add column gender count groupby by location
odf.annotate(
    gender_count={'gender': {'name':'count'}},
    gender_count_by_location={'location': {'gender': 'count'}},
    age_average_by_location={'location': {'age': 'mean'}},
)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
      <th>gender_count</th>
      <th>gender_count_by_location</th>
      <th>age_average_by_location</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>72.4</td>
      <td>8</td>
      <td>4.0</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12.0</td>
      <td>8</td>
      <td>3.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>28</td>
      <td>F</td>
      <td>China</td>
      <td>Lyn</td>
      <td>0.0</td>
      <td>3</td>
      <td>3.0</td>
      <td>26.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>M</td>
      <td>China</td>
      <td>Xiaomi</td>
      <td>90.0</td>
      <td>8</td>
      <td>3.0</td>
      <td>26.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2.5</td>
      <td>8</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>10.0</td>
      <td>8</td>
      <td>3.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>12</td>
      <td>F</td>
      <td>Korea</td>
      <td>Lee</td>
      <td>20.0</td>
      <td>3</td>
      <td>4.0</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
      <td>8</td>
      <td>3.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
      <td>8</td>
      <td>3.0</td>
      <td>26.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>2.0</td>
      <td>8</td>
      <td>4.0</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>86.0</td>
      <td>3</td>
      <td>4.0</td>
      <td>24.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
odf.aggregate(gender_count={'location': {'gender':'count'}})
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>gender_count</th>
    </tr>
    <tr>
      <th>location</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>China</th>
      <td>3</td>
    </tr>
    <tr>
      <th>Korea</th>
      <td>4</td>
    </tr>
    <tr>
      <th>USA</th>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>



### 4.4 orderby 


```python
odf = pd.DataFrame(userTable)
```


```python
odf.select('location', 'gender', 'name', 'age').orderby('location', '-age')
# ordered by location ascending, but age descending
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>location</th>
      <th>gender</th>
      <th>name</th>
      <th>age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8</th>
      <td>China</td>
      <td>M</td>
      <td>ChinChin</td>
      <td>35</td>
    </tr>
    <tr>
      <th>2</th>
      <td>China</td>
      <td>F</td>
      <td>Lyn</td>
      <td>28</td>
    </tr>
    <tr>
      <th>3</th>
      <td>China</td>
      <td>M</td>
      <td>Xiaomi</td>
      <td>15</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Korea</td>
      <td>F</td>
      <td>Kim</td>
      <td>37</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Korea</td>
      <td>M</td>
      <td>Cho</td>
      <td>29</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Korea</td>
      <td>M</td>
      <td>Hong</td>
      <td>18</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Korea</td>
      <td>F</td>
      <td>Lee</td>
      <td>12</td>
    </tr>
    <tr>
      <th>7</th>
      <td>USA</td>
      <td>M</td>
      <td>Michle</td>
      <td>24</td>
    </tr>
    <tr>
      <th>1</th>
      <td>USA</td>
      <td>M</td>
      <td>Charse</td>
      <td>19</td>
    </tr>
    <tr>
      <th>5</th>
      <td>USA</td>
      <td>M</td>
      <td>Smith</td>
      <td>17</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NaN</td>
      <td>M</td>
      <td>Park</td>
      <td>29</td>
    </tr>
  </tbody>
</table>
</div>



### 4.5 top


```python
odf = pd.DataFrame(userTable)
```


```python
odf.top('age', ntop=0.1)
# ret top 10% of age
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>86.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
odf.top('point', ntop=0.2, ascending=True)
# ret smallest 20% of point at the bottom
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2.5</td>
    </tr>
  </tbody>
</table>
</div>




```python
# get top 3 order users
odf.top('age', 3)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>86.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2.5</td>
    </tr>
  </tbody>
</table>
</div>




```python
# get bottom 4 younger users
odf.top('age', 4, ascending=True)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6</th>
      <td>12</td>
      <td>F</td>
      <td>Korea</td>
      <td>Lee</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>M</td>
      <td>China</td>
      <td>Xiaomi</td>
      <td>90.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>72.4</td>
    </tr>
  </tbody>
</table>
</div>




```python
odf.top('point') # max point user
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>M</td>
      <td>China</td>
      <td>Xiaomi</td>
      <td>90.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
odf.top('point', ascending=True) # min point user
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
  </tbody>
</table>
</div>



### 4.6 types


```python
odf = pd.DataFrame(userTable)
```


```python
odf.types(point=int, age=str)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>72</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12</td>
    </tr>
    <tr>
      <th>2</th>
      <td>28</td>
      <td>F</td>
      <td>China</td>
      <td>Lyn</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>M</td>
      <td>China</td>
      <td>Xiaomi</td>
      <td>90</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>10</td>
    </tr>
    <tr>
      <th>6</th>
      <td>12</td>
      <td>F</td>
      <td>Korea</td>
      <td>Lee</td>
      <td>20</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>2</td>
    </tr>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>86</td>
    </tr>
  </tbody>
</table>
</div>



### 4.6 map


```python
odf = pd.DataFrame(userTable)
```


```python
odf.map(gender={'M': 'Mail', 'F': 'Femail'})
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>Mail</td>
      <td>Korea</td>
      <td>Hong</td>
      <td>72.4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>Mail</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>28</td>
      <td>Femail</td>
      <td>China</td>
      <td>Lyn</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>Mail</td>
      <td>China</td>
      <td>Xiaomi</td>
      <td>90.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>Mail</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2.5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>Mail</td>
      <td>USA</td>
      <td>Smith</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>12</td>
      <td>Femail</td>
      <td>Korea</td>
      <td>Lee</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>Mail</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>Mail</td>
      <td>China</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>Mail</td>
      <td>Korea</td>
      <td>Cho</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>Femail</td>
      <td>Korea</td>
      <td>Kim</td>
      <td>86.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
odf.map(location={'Korea': 'KOR', 'China': 'CHN'})
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18</td>
      <td>M</td>
      <td>KOR</td>
      <td>Hong</td>
      <td>72.4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19</td>
      <td>M</td>
      <td>USA</td>
      <td>Charse</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>28</td>
      <td>F</td>
      <td>CHN</td>
      <td>Lyn</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>M</td>
      <td>CHN</td>
      <td>Xiaomi</td>
      <td>90.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29</td>
      <td>M</td>
      <td>NaN</td>
      <td>Park</td>
      <td>-2.5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17</td>
      <td>M</td>
      <td>USA</td>
      <td>Smith</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>12</td>
      <td>F</td>
      <td>KOR</td>
      <td>Lee</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>24</td>
      <td>M</td>
      <td>USA</td>
      <td>Michle</td>
      <td>75.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>35</td>
      <td>M</td>
      <td>CHN</td>
      <td>ChinChin</td>
      <td>-9.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>29</td>
      <td>M</td>
      <td>KOR</td>
      <td>Cho</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>37</td>
      <td>F</td>
      <td>KOR</td>
      <td>Kim</td>
      <td>86.0</td>
    </tr>
  </tbody>
</table>
</div>



# 5. Utilities

### 5.1 to_dictlist, to_tuplelist


```python
odf.to_dictlist()[:5]
```




    [{'age': 18,
      'gender': 'M',
      'location': 'Korea',
      'name': 'Hong',
      'point': 72.4},
     {'age': 19,
      'gender': 'M',
      'location': 'USA',
      'name': 'Charse',
      'point': 12.0},
     {'age': 28, 'gender': 'F', 'location': 'China', 'name': 'Lyn', 'point': 0.0},
     {'age': 15,
      'gender': 'M',
      'location': 'China',
      'name': 'Xiaomi',
      'point': 90.0},
     {'age': 29, 'gender': 'M', 'location': nan, 'name': 'Park', 'point': -2.5}]




```python
odf.to_tuplelist()[:5]
```




    [(18, 'M', 'Korea', 'Hong', 72.4),
     (19, 'M', 'USA', 'Charse', 12.0),
     (28, 'F', 'China', 'Lyn', 0.0),
     (15, 'M', 'China', 'Xiaomi', 90.0),
     (29, 'M', nan, 'Park', -2.5)]




```python
odf.to_tuplelist(header=True)[:5] #with fields names on header
```




    [('age', 'gender', 'location', 'name', 'point'),
     (18, 'M', 'Korea', 'Hong', 72.4),
     (19, 'M', 'USA', 'Charse', 12.0),
     (28, 'F', 'China', 'Lyn', 0.0),
     (15, 'M', 'China', 'Xiaomi', 90.0)]



### 5.2 compare 


```python
before = [
    {'name': 'Charse', 'gender': 'M', 'age': 19, 'location': 'USA'},
    {'name': 'Lyn', 'gender': 'F', 'age': 28, 'location': 'China'},
    {'name': 'Park', 'gender': 'M', 'age': 28, },
    {'name': 'Lee', 'gender': 'M', 'age': 12, 'location': 'Korea'},
    {'name': 'Cho', 'gender': 'M', 'age': 29, 'location': 'Korea'},
    {'name': 'Kim', 'gender': 'F', 'age': 37, 'location': 'Korea'},
]

after = [
    {'name': 'Hong', 'gender': 'M', 'age': 17, 'location': 'Korea', 'point': 72.4},
    {'name': 'Charse', 'gender': 'F', 'age': 19, 'location': 'USA', 'point': 12},
    {'name': 'Lyn', 'gender': 'F', 'age': 28, 'location': 'China', 'point': 0},
    {'name': 'Park', 'gender': 'F', 'age': 29, 'point': -2.5},
    {'name': 'Smith', 'gender': 'M', 'age': 22, 'location': 'USA', 'point': 10},
    {'name': 'ChinChin', 'gender': 'M', 'age': 35, 'location': 'China','point': -9},
    {'name': 'Cho', 'gender': 'M', 'age': 29, 'location': 'Canada','point': 2},
    {'name': 'Kimc', 'gender': 'M', 'age': 45, 'location': 'Korea','point': 3},
]
```

 - skipped not common column


```python
before_df = pd.DataFrame(before)
```


```python
after_df = pd.DataFrame(after)
```


```python
comp_result_set = before_df.compare(after, pk='name')
```


```python
comp_result_set.changes # summrise what column and indexes ara changed
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>pk</th>
      <th>column</th>
      <th>from</th>
      <th>to</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Charse</td>
      <td>gender</td>
      <td>M</td>
      <td>F</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Park</td>
      <td>age</td>
      <td>28</td>
      <td>29</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Park</td>
      <td>gender</td>
      <td>M</td>
      <td>F</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Cho</td>
      <td>location</td>
      <td>Korea</td>
      <td>Canada</td>
    </tr>
  </tbody>
</table>
</div>




```python
comp_result_set.added # added member
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
    </tr>
    <tr>
      <th>name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>ChinChin</th>
      <td>35</td>
      <td>M</td>
      <td>China</td>
    </tr>
    <tr>
      <th>Hong</th>
      <td>17</td>
      <td>M</td>
      <td>Korea</td>
    </tr>
    <tr>
      <th>Kimc</th>
      <td>45</td>
      <td>M</td>
      <td>Korea</td>
    </tr>
    <tr>
      <th>Smith</th>
      <td>22</td>
      <td>M</td>
      <td>USA</td>
    </tr>
  </tbody>
</table>
</div>




```python
comp_result_set.deleted # deleted member
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>gender</th>
      <th>location</th>
    </tr>
    <tr>
      <th>name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Kim</th>
      <td>37</td>
      <td>F</td>
      <td>Korea</td>
    </tr>
    <tr>
      <th>Lee</th>
      <td>12</td>
      <td>M</td>
      <td>Korea</td>
    </tr>
  </tbody>
</table>
</div>


