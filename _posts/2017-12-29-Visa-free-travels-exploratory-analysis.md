---
layout: post
title: Visa free travels - exploratory analysis with Kaggle dataset
tags: python numpy pandas matplotlib jupyter-notebook
---


This December I am celebrating 3 months anniversary with Python. On the last weekend of September I participated in PyLove workshop which was my first ever encounter with programming. Many lessons, few books and an online course later I dared to download my first dataset found on Kaggle and play around with it. I chose [Visa Free Travel by Ctizenship 2016, Inequality in world citizenship](https://www.kaggle.com/sdorius/visafree2016) and added more information using [Countries REST API](https://restcountries.eu/).

The problem I wanted to tackle was to measure the persisting inequality and see whether countries with similar rank share some characteristics with each other. 

##  #1 DATA PREPARATION

**Input:**
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import requests

%matplotlib inline
sns.set()
```


```python
# Loading Kaggle data set
visa = pd.read_csv('VisaFreeScore.csv')
```


```python
visa.head()
```



**Output:**
<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>country</th>
      <th>visarank</th>
      <th>visafree</th>
      <th>visaonarrive</th>
      <th>visawelc</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Germany</td>
      <td>1.0</td>
      <td>157.0</td>
      <td>117.0</td>
      <td>84.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Sweden</td>
      <td>1.0</td>
      <td>157.0</td>
      <td>117.0</td>
      <td>84.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Finland</td>
      <td>2.0</td>
      <td>156.0</td>
      <td>117.0</td>
      <td>85.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>France</td>
      <td>2.0</td>
      <td>156.0</td>
      <td>116.0</td>
      <td>84.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Italy</td>
      <td>2.0</td>
      <td>156.0</td>
      <td>117.0</td>
      <td>84.0</td>
    </tr>
  </tbody>
</table>
</div>



**Input:**
```python
visa.info()
```
**Output:**

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 200 entries, 0 to 199
    Data columns (total 5 columns):
    country         199 non-null object
    visarank        199 non-null float64
    visafree        199 non-null float64
    visaonarrive    199 non-null float64
    visawelc        199 non-null float64
    dtypes: float64(4), object(1)
    memory usage: 7.9+ KB


**Input:**
```python
# Loading country data from open API to a json file
countries = requests.get('https://restcountries.eu/rest/v2/all')
countries = countries.json()
```


```python
# Creating empty lists of information I chose and filling them with data from API
name = []
borders = []
region = []
latlng = []
area = []
population = []
gini = []

x = 0
for country in countries:
    name.append(countries[x]['name'])
    borders.append(countries[x]['borders'])
    region.append(countries[x]['region'])
    latlng.append(countries[x]['latlng'])
    area.append(countries[x]['area'])
    population.append(countries[x]['population'])
    gini.append(countries[x]['gini'])
    x += 1
```


```python
# Change from list of borders into number of shared boarders for each country
y = 0 
for border in borders:
    borders[y] = len(border)
    y += 1
```


```python
# I want to separate latlng column, but couldn't simply loop through it as it has some empty lists. 
# Finding where latlng doesn't contain 2 values
for value in latlng:
    if len(value) == 0:
        print(latlng.index(value))
    elif len(value) == 1:
        print(latlng.index(value))
```
**Output:**
    33


**Input:**
```python
latlng[33] = ['', '']
```


```python
lat = []
lng = []
z = 0
for value in latlng:
    lat.append(latlng[z][0])
    lng.append(latlng[z][1])
    z += 1
```


```python
# Creating Pandas DataFrame from API data
df_countries = pd.DataFrame(
    {'country': name,
     'borders': borders,
     'region': region,
     'lat': lat,
     'lng': lng,
     'area': area,
     'population': population,
     'gini':gini
    })
```


```python
df_countries.info()
```

**Output:**

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 250 entries, 0 to 249
    Data columns (total 8 columns):
    area          240 non-null float64
    borders       250 non-null int64
    country       250 non-null object
    gini          153 non-null float64
    lat           250 non-null object
    lng           250 non-null object
    population    250 non-null int64
    region        250 non-null object
    dtypes: float64(2), int64(2), object(4)
    memory usage: 15.7+ KB


**Input:**
```python
# Merging two datasets into one table on country row
vc = pd.merge(visa, df_countries, how='inner', on='country')
vc = vc.set_index('country')
vc.info()
```

**Output:**

    <class 'pandas.core.frame.DataFrame'>
    Index: 178 entries, Germany to Afghanistan
    Data columns (total 11 columns):
    visarank        178 non-null float64
    visafree        178 non-null float64
    visaonarrive    178 non-null float64
    visawelc        178 non-null float64
    area            178 non-null float64
    borders         178 non-null int64
    gini            140 non-null float64
    lat             178 non-null object
    lng             178 non-null object
    population      178 non-null int64
    region          178 non-null object
    dtypes: float64(6), int64(2), object(3)
    memory usage: 16.7+ KB


## #2 EXPLORATORY ANALYSIS

```python
vc[['visarank', 'visafree', 'visaonarrive', 'visawelc', 'region']].head(10)
```


<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>visarank</th>
      <th>visafree</th>
      <th>visaonarrive</th>
      <th>visawelc</th>
      <th>region</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Germany</th>
      <td>1.0</td>
      <td>157.0</td>
      <td>117.0</td>
      <td>84.0</td>
      <td>Europe</td>
    </tr>
    <tr>
      <th>Sweden</th>
      <td>1.0</td>
      <td>157.0</td>
      <td>117.0</td>
      <td>84.0</td>
      <td>Europe</td>
    </tr>
    <tr>
      <th>Finland</th>
      <td>2.0</td>
      <td>156.0</td>
      <td>117.0</td>
      <td>85.0</td>
      <td>Europe</td>
    </tr>
    <tr>
      <th>France</th>
      <td>2.0</td>
      <td>156.0</td>
      <td>116.0</td>
      <td>84.0</td>
      <td>Europe</td>
    </tr>
    <tr>
      <th>Italy</th>
      <td>2.0</td>
      <td>156.0</td>
      <td>117.0</td>
      <td>84.0</td>
      <td>Europe</td>
    </tr>
    <tr>
      <th>Spain</th>
      <td>2.0</td>
      <td>156.0</td>
      <td>115.0</td>
      <td>84.0</td>
      <td>Europe</td>
    </tr>
    <tr>
      <th>Switzerland</th>
      <td>2.0</td>
      <td>156.0</td>
      <td>116.0</td>
      <td>84.0</td>
      <td>Europe</td>
    </tr>
    <tr>
      <th>Belgium</th>
      <td>3.0</td>
      <td>155.0</td>
      <td>115.0</td>
      <td>84.0</td>
      <td>Europe</td>
    </tr>
    <tr>
      <th>Denmark</th>
      <td>3.0</td>
      <td>155.0</td>
      <td>116.0</td>
      <td>83.0</td>
      <td>Europe</td>
    </tr>
    <tr>
      <th>Netherlands</th>
      <td>3.0</td>
      <td>155.0</td>
      <td>115.0</td>
      <td>84.0</td>
      <td>Europe</td>
    </tr>
  </tbody>
</table>
</div>




**Input:**
```python
vc[['visarank', 'visafree', 'visaonarrive', 'visawelc', 'region']].tail(10)
```


**Output:**

<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>visarank</th>
      <th>visafree</th>
      <th>visaonarrive</th>
      <th>visawelc</th>
      <th>region</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Sri Lanka</th>
      <td>85.0</td>
      <td>38.0</td>
      <td>14.0</td>
      <td>181.0</td>
      <td>Asia</td>
    </tr>
    <tr>
      <th>Bangladesh</th>
      <td>86.0</td>
      <td>37.0</td>
      <td>16.0</td>
      <td>174.0</td>
      <td>Asia</td>
    </tr>
    <tr>
      <th>Ethiopia</th>
      <td>86.0</td>
      <td>37.0</td>
      <td>6.0</td>
      <td>41.0</td>
      <td>Africa</td>
    </tr>
    <tr>
      <th>Libya</th>
      <td>86.0</td>
      <td>37.0</td>
      <td>8.0</td>
      <td>3.0</td>
      <td>Africa</td>
    </tr>
    <tr>
      <th>Sudan</th>
      <td>86.0</td>
      <td>37.0</td>
      <td>5.0</td>
      <td>10.0</td>
      <td>Africa</td>
    </tr>
    <tr>
      <th>South Sudan</th>
      <td>87.0</td>
      <td>36.0</td>
      <td>7.0</td>
      <td>5.0</td>
      <td>Africa</td>
    </tr>
    <tr>
      <th>Somalia</th>
      <td>89.0</td>
      <td>32.0</td>
      <td>5.0</td>
      <td>0.0</td>
      <td>Africa</td>
    </tr>
    <tr>
      <th>Iraq</th>
      <td>90.0</td>
      <td>31.0</td>
      <td>5.0</td>
      <td>1.0</td>
      <td>Asia</td>
    </tr>
    <tr>
      <th>Pakistan</th>
      <td>91.0</td>
      <td>28.0</td>
      <td>6.0</td>
      <td>9.0</td>
      <td>Asia</td>
    </tr>
    <tr>
      <th>Afghanistan</th>
      <td>92.0</td>
      <td>25.0</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>Asia</td>
    </tr>
  </tbody>
</table>
</div>



```python
vc.info()
```


<class 'pandas.core.frame.DataFrame'>
    Index: 178 entries, Germany to Afghanistan
    Data columns (total 11 columns):
    visarank        178 non-null float64
    visafree        178 non-null float64
    visaonarrive    178 non-null float64
    visawelc        178 non-null float64
    area            178 non-null float64
    borders         178 non-null int64
    gini            140 non-null float64
    lat             178 non-null object
    lng             178 non-null object
    population      178 non-null int64
    region          178 non-null object
    dtypes: float64(6), int64(2), object(3)
    memory usage: 16.7+ KB

* First look at the data shows that top 10 countries are dominated by Europe, whereas the lowest rank countries represent Asia and Africa.


* The lowest rank is 92 which means that some of 178 countries share the same rank. 


* Gini coefficient is the only variable with missing values.

---


```python
vc[['visarank', 'visafree', 'visaonarrive', 'visawelc']].corr()
```


<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>visarank</th>
      <th>visafree</th>
      <th>visaonarrive</th>
      <th>visawelc</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>visarank</th>
      <td>1.000000</td>
      <td>-0.996567</td>
      <td>-0.990366</td>
      <td>-0.125559</td>
    </tr>
    <tr>
      <th>visafree</th>
      <td>-0.996567</td>
      <td>1.000000</td>
      <td>0.995280</td>
      <td>0.117318</td>
    </tr>
    <tr>
      <th>visaonarrive</th>
      <td>-0.990366</td>
      <td>0.995280</td>
      <td>1.000000</td>
      <td>0.131792</td>
    </tr>
    <tr>
      <th>visawelc</th>
      <td>-0.125559</td>
      <td>0.117318</td>
      <td>0.131792</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



* Visa rank is calculated on the basis of number of visa free destinations and possibility of acquiring visa on arrive. Therefore correlation between those 3 columns is almost 1.


* Visawelc - number of countries that are not required to have a visa while visiting shows almost no connection with the rank.

---


```python
((vc['visarank'].groupby(by=vc['region']).count()/178)*100).plot(kind='bar', alpha=0.7)
plt.title('Percentage of countries by region');
```

![png]({{ "/assets/images/2017-12-29-Visa-free-travels-exploratory-analysis_22_0.png"}})


---

```python
plt.hist(vc['visarank'], bins=20, ec='white', alpha=0.7)
plt.title('Distribution of visa rank')
plt.xlim(vc['visarank'].min(), vc['visarank'].max());
```

![png]({{ "/assets/images/2017-12-29-Visa-free-travels-exploratory-analysis_23_0.png"}})


* The lower the visa rank, the higher freedom of travel citizens of a country have. This histogram shows inequality in free travelling. There are two local max values - for countries with highest degree of freedom to travel, and for countries ranking just under 80, meaning many restrictions by visa requirements. 

---

```python
plt.hist(vc['visafree'], bins=20, ec='white', alpha=0.7)
plt.title('Distribution of visa free destinations per country')
plt.xlim(vc['visafree'].min(), vc['visafree'].max());
```

![png]({{ "/assets/images/2017-12-29-Visa-free-travels-exploratory-analysis_25_0.png"}})


* Distribution of visa free destinations allows to put a measure on inequality shown on the previous chart. Highest degree of freedom to travel means being able to visit almost 160 countries all over the world with no formal requirements. Countries with most restricions can travel to only about one third of this number.

---

```python
fig = plt.figure(figsize=(8,5))
fig.add_subplot(111)

vc['visafree'].groupby(by=vc['region']).mean().plot(kind='bar', color='#19436B', position=0, width=0.3, alpha=0.7, label = 'Average number of visa free destinations')
vc['visawelc'].groupby(by=vc['region']).mean().plot(kind='bar', position=1, width=0.3, alpha=0.7, label = 'Average number of countries accepted visa free')
plt.legend();
```


![png]({{ "/assets/images//2017-12-29-Visa-free-travels-exploratory-analysis_27_0.png"}})


* Africa and Asia are regions, where citizens of each country have lowest number of visa free destinations.


* European citizens have strong advantage over other regions.


* At the same time Europe is a region that on average accepts the lowest number of visitors without visa. 


* Americas' citizens also can travel visa free more than they offer visa free entries, however the difference is visibly smaller.


* All the other regions have an opposite ratio and the biggest difference between those values persists among African countries.

---

```python
vc1 = pd.DataFrame()
vc1['welcome_count'] = vc['visawelc'].groupby(by=vc['region']).sum()
vc1['free_count'] = vc['visafree'].groupby(by=vc['region']).sum()
vc1['free_welcome_ratio'] = vc1['free_count'] / vc1['welcome_count'] 
vc1
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>welcome_count</th>
      <th>free_count</th>
      <th>free_welcome_ratio</th>
    </tr>
    <tr>
      <th>region</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Africa</th>
      <td>4106.0</td>
      <td>2735.0</td>
      <td>0.666098</td>
    </tr>
    <tr>
      <th>Americas</th>
      <td>3226.0</td>
      <td>3426.0</td>
      <td>1.061996</td>
    </tr>
    <tr>
      <th>Asia</th>
      <td>3583.0</td>
      <td>2999.0</td>
      <td>0.837008</td>
    </tr>
    <tr>
      <th>Europe</th>
      <td>3296.0</td>
      <td>5742.0</td>
      <td>1.742112</td>
    </tr>
    <tr>
      <th>Oceania</th>
      <td>1308.0</td>
      <td>1191.0</td>
      <td>0.910550</td>
    </tr>
  </tbody>
</table>
</div>



* Free_welcome_ratio columns shows how many countries can an average citizen of a region travel visa free for welcoming one country without such requirement.

---


```python
vc[vc['visawelc']==0][['visarank', 'visawelc', 'region']]
```



<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>visarank</th>
      <th>visawelc</th>
      <th>region</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Turkmenistan</th>
      <td>74.0</td>
      <td>0.0</td>
      <td>Asia</td>
    </tr>
    <tr>
      <th>Somalia</th>
      <td>89.0</td>
      <td>0.0</td>
      <td>Africa</td>
    </tr>
    <tr>
      <th>Afghanistan</th>
      <td>92.0</td>
      <td>0.0</td>
      <td>Asia</td>
    </tr>
  </tbody>
</table>
</div>





```python
vc[vc['visawelc']==vc['visawelc'].max()][['visarank', 'visawelc', 'region']]
```



<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>visarank</th>
      <th>visawelc</th>
      <th>region</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Seychelles</th>
      <td>24.0</td>
      <td>198.0</td>
      <td>Africa</td>
    </tr>
    <tr>
      <th>Samoa</th>
      <td>35.0</td>
      <td>198.0</td>
      <td>Oceania</td>
    </tr>
    <tr>
      <th>Timor-Leste</th>
      <td>47.0</td>
      <td>198.0</td>
      <td>Asia</td>
    </tr>
    <tr>
      <th>Tuvalu</th>
      <td>51.0</td>
      <td>198.0</td>
      <td>Oceania</td>
    </tr>
    <tr>
      <th>Uganda</th>
      <td>67.0</td>
      <td>198.0</td>
      <td>Africa</td>
    </tr>
    <tr>
      <th>Mauritania</th>
      <td>71.0</td>
      <td>198.0</td>
      <td>Africa</td>
    </tr>
    <tr>
      <th>Togo</th>
      <td>72.0</td>
      <td>198.0</td>
      <td>Africa</td>
    </tr>
    <tr>
      <th>Cambodia</th>
      <td>75.0</td>
      <td>198.0</td>
      <td>Asia</td>
    </tr>
    <tr>
      <th>Guinea-Bissau</th>
      <td>75.0</td>
      <td>198.0</td>
      <td>Africa</td>
    </tr>
    <tr>
      <th>Madagascar</th>
      <td>76.0</td>
      <td>198.0</td>
      <td>Africa</td>
    </tr>
    <tr>
      <th>Mozambique</th>
      <td>76.0</td>
      <td>198.0</td>
      <td>Africa</td>
    </tr>
    <tr>
      <th>Comoros</th>
      <td>78.0</td>
      <td>198.0</td>
      <td>Africa</td>
    </tr>
    <tr>
      <th>Burundi</th>
      <td>82.0</td>
      <td>198.0</td>
      <td>Africa</td>
    </tr>
  </tbody>
</table>
</div>



* 13 countries welcome all other without visa requirements, 3 countries allow no one to enter visa free.

---

```python
plt.figure(figsize=(4,4))
vc['population'].groupby(np.where(vc['visafree']>vc['visawelc'], 'In advantage', 'In disadvantage')).sum().plot(kind='pie', label='')
plt.title('Share of people globally by advantage and disadvantage in ratio of visa free destinations to welcoming visa free visitors');
```


![png]({{ "/assets/images/2017-12-29-Visa-free-travels-exploratory-analysis_34_0.png"}})


* Number of citizens of countries that are in discriminating travelling agreements is approaching a quarter of population globally.

---

```python
fig, axes = plt.subplots(nrows=2, ncols=2, figsize=(12,7), )
vc[['population', 'visafree']].plot(ax = axes[0,0], x='population', y='visafree', kind='scatter')
vc[['gini', 'visafree']].plot(ax = axes[0,1], x='gini', y='visafree', kind='scatter')
vc[['borders', 'visafree']].plot(ax = axes[1,0], x='borders', y='visafree', kind='scatter')
vc[['area', 'visafree']].plot(ax = axes[1,1], x='area', y='visafree', kind='scatter');
```


![png]({{ "/assets/images/2017-12-29-Visa-free-travels-exploratory-analysis_36_0.png"}})


The above diagrams check for patterns between number of visa free destinations and country characteristics.
* There seems to be little conection between population size and visa free destinations.


* Gini coefficient values are scattered, however there are two visible groups: gini score between 25 and 40 is paired with high number of visa free destinations (between 140 and 160 countries. Gini score between 30 and 45 is paired with lower number of visa free destinations (between 40 and 70 countries).


* For different numbers of borders, number of visa free destinations tends to have full spectrum from very low to very high values.


* There seems to be some pattern between area and visa free destinations. Ignoring extreme area values, with bigger area number of visa free destinations tends to cummulate around 30 to 70 countries.

---


```python
fig, axes = plt.subplots(nrows=2, ncols=2, figsize=(12,7))
vc[['population', 'visawelc']].plot(ax = axes[0,0], x='population', y='visawelc', kind='scatter')
vc[['gini', 'visawelc']].plot(ax = axes[0,1], x='gini', y='visawelc', kind='scatter')
vc[['borders', 'visawelc']].plot(ax = axes[1,0], x='borders', y='visawelc', kind='scatter')
vc[['area', 'visawelc']].plot(ax = axes[1,1], x='area', y='visawelc', kind='scatter');
```


![png]({{ "/assets/images//2017-12-29-Visa-free-travels-exploratory-analysis_38_0.png"}})


The above diagrams check for patterns between number of welcoming visa free visitors and country characteristics.
* Population again seems not to show any strong pattern against welcoming visa free visitors.


* Low Gini coefficient (up to 30) is connected with visibly lower number of accepting visitors with no visa (around 80 countries). Only with Gini score higher than 30 we can observe full spectrum of visawelc values.


* It's difficult to observe a strong pattern between visawelc and number of borders. However interestingly, high numbers of allowing visa free visitors is more common for up to 2 shared borders. For higher values there are only single countries with such result.


* Bigger area countries tend to allow up to 100 countries' citizents with no visa requirement.


ipynb file with full tables can be found [here](https://github.com/dorotamierzwa/Data-analysis-projects/blob/master/Visa%20free%20travels%20-%20exploratory%20analysis%20with%20Kaggle%20dataset.ipynb).
