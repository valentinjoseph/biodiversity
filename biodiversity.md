# Biodiversity Project

Let's work as a biodiversity analyst and aanaylse data from national parks

Note: The data for this project is *inspired* by real data, but is mostly fictional.

The questions we will try to answer:

1- are certain species more likely to be endangered?

2- How many species are endangered, threatened, in recovery, species of concern?

3- How many total sheep observations (across all species) were made at each national park?

4- How many weeks would you need to observe sheep at Bryce National Park in order to observe enough sheep?  How many weeks would you need to observe at Yellowstone National Park to observe enough sheep?

Import the modules


```python
from matplotlib import pyplot as plt
import pandas as pd
```

There are two csv files: `species_info.csv` and `observations.csv` let's start with the `species_info.csv` data set 

# Species


```python
species = pd.read_csv("species_info.csv")
```


```python
species.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>scientific_name</th>
      <th>common_names</th>
      <th>conservation_status</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Mammal</td>
      <td>Clethrionomys gapperi gapperi</td>
      <td>Gapper's Red-Backed Vole</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Mammal</td>
      <td>Bos bison</td>
      <td>American Bison, Bison</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Mammal</td>
      <td>Bos taurus</td>
      <td>Aurochs, Aurochs, Domestic Cattle (Feral), Dom...</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Mammal</td>
      <td>Ovis aries</td>
      <td>Domestic Sheep, Mouflon, Red Sheep, Sheep (Feral)</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Mammal</td>
      <td>Cervus elaphus</td>
      <td>Wapiti Or Elk</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



Let's answer some questions.

How many different species are in the `species` DataFrame?


```python
species_count= species.scientific_name.nunique()
print(species_count)
```

    5541


What are the different values of `category` in `species`?


```python
species_category = species.category.unique()
print(species_category)
```

    ['Mammal' 'Bird' 'Reptile' 'Amphibian' 'Fish' 'Vascular Plant'
     'Nonvascular Plant']


What are the different values of `conservation_status`?


```python
conserv_stats_val= species.conservation_status.unique()
print(conserv_stats_val)
```

    [nan 'Species of Concern' 'Endangered' 'Threatened' 'In Recovery']


Let's start doing some analysis!

I'd like to count up how many species meet each of conservation status values.


```python
status_counts = species.groupby('conservation_status').scientific_name.nunique().reset_index()
print(status_counts)
```

      conservation_status  scientific_name
    0          Endangered               15
    1         In Recovery                4
    2  Species of Concern              151
    3          Threatened               10


We saw earlier that there are around 5541 species so in the above chart, the NAN values are missing, let's add them


```python
species.fillna('No Intervention', inplace=True)
```


```python
status_counts = species.groupby('conservation_status').scientific_name.nunique().reset_index()
print(status_counts)
```

      conservation_status  scientific_name
    0          Endangered               15
    1         In Recovery                4
    2     No Intervention             5363
    3  Species of Concern              151
    4          Threatened               10


Let's create a bar chart.  We first need to sort the columns by how many species are in each categories.


```python
protection_counts = species.groupby('conservation_status')\
    .scientific_name.nunique().reset_index()\
    .sort_values(by='scientific_name')
```

Now let's create a bar chart!


```python
plt.figure(figsize=(10,8))
ax=plt.subplot()
plt.bar(range(len(protection_counts)), protection_counts.scientific_name.values)
ax.set_xticks(range(len(protection_counts)))
ax.set_xticklabels(protection_counts.conservation_status.values)
plt.xlabel("Species")
plt.ylabel("Number of species")
plt.title("Conservation status per species")
plt.show()
plt.savefig('Conservation status per species.png')
```


    
![png](output_23_0.png)
    



    <Figure size 432x288 with 0 Axes>


Are certain types of species more likely to be endangered?


```python
species['is_protected'] = species.conservation_status != 'No Intervention'
```


```python
category_counts = species.groupby(['category', 'is_protected']).scientific_name.nunique().reset_index()
print(category_counts)
```

                 category  is_protected  scientific_name
    0           Amphibian         False               72
    1           Amphibian          True                7
    2                Bird         False              413
    3                Bird          True               75
    4                Fish         False              115
    5                Fish          True               11
    6              Mammal         False              146
    7              Mammal          True               30
    8   Nonvascular Plant         False              328
    9   Nonvascular Plant          True                5
    10            Reptile         False               73
    11            Reptile          True                5
    12     Vascular Plant         False             4216
    13     Vascular Plant          True               46



```python
category_counts.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>is_protected</th>
      <th>scientific_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Amphibian</td>
      <td>False</td>
      <td>72</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Amphibian</td>
      <td>True</td>
      <td>7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Bird</td>
      <td>False</td>
      <td>413</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Bird</td>
      <td>True</td>
      <td>75</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Fish</td>
      <td>False</td>
      <td>115</td>
    </tr>
  </tbody>
</table>
</div>




```python
category_pivot = category_counts.pivot(columns='is_protected',
                                      index='category',
                                      values='scientific_name')\
                                .reset_index()
```


```python
category_pivot
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>is_protected</th>
      <th>category</th>
      <th>False</th>
      <th>True</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Amphibian</td>
      <td>72</td>
      <td>7</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bird</td>
      <td>413</td>
      <td>75</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Fish</td>
      <td>115</td>
      <td>11</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Mammal</td>
      <td>146</td>
      <td>30</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Nonvascular Plant</td>
      <td>328</td>
      <td>5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Reptile</td>
      <td>73</td>
      <td>5</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Vascular Plant</td>
      <td>4216</td>
      <td>46</td>
    </tr>
  </tbody>
</table>
</div>




```python
category_pivot.columns = ['category', 'not_protected', 'protected']
```


```python
category_pivot['percent_protected']= category_pivot.protected / (category_pivot.protected + category_pivot.not_protected)
```


```python
category_pivot
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>not_protected</th>
      <th>protected</th>
      <th>percent_protected</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Amphibian</td>
      <td>72</td>
      <td>7</td>
      <td>0.088608</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bird</td>
      <td>413</td>
      <td>75</td>
      <td>0.153689</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Fish</td>
      <td>115</td>
      <td>11</td>
      <td>0.087302</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Mammal</td>
      <td>146</td>
      <td>30</td>
      <td>0.170455</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Nonvascular Plant</td>
      <td>328</td>
      <td>5</td>
      <td>0.015015</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Reptile</td>
      <td>73</td>
      <td>5</td>
      <td>0.064103</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Vascular Plant</td>
      <td>4216</td>
      <td>46</td>
      <td>0.010793</td>
    </tr>
  </tbody>
</table>
</div>



It looks like species in category `Mammal` are more likely to be endangered than species in `Bird`.  We're going to do a significance test to see if this statement is true. Since it is a categorical data and we'll compare two samples, we need to do a chi squared test.


```python
from scipy.stats import chi2_contingency
contingency = [[30, 146],
              [75, 413]]
```


```python
chi2_contingency(contingency)
```




    (0.1617014831654557,
     0.6875948096661336,
     1,
     array([[ 27.8313253, 148.1686747],
            [ 77.1686747, 410.8313253]]))



It looks like this difference isn't significant!

Let's test another.  Is the difference between `Reptile` and `Mammal` significant?


```python
contingency = [[30, 146],
               [5, 73]]
chi2_contingency(contingency)
```




    (4.289183096203645,
     0.03835559022969898,
     1,
     array([[ 24.2519685, 151.7480315],
            [ 10.7480315,  67.2519685]]))



Yes! It looks like there is a significant difference between `Reptile` and `Mammal`!

# Observations


```python
observations = pd.read_csv("observations.csv")
observations.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>scientific_name</th>
      <th>park_name</th>
      <th>observations</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Vicia benghalensis</td>
      <td>Great Smoky Mountains National Park</td>
      <td>68</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Neovison vison</td>
      <td>Great Smoky Mountains National Park</td>
      <td>77</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Prunus subcordata</td>
      <td>Yosemite National Park</td>
      <td>138</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Abutilon theophrasti</td>
      <td>Bryce National Park</td>
      <td>84</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Githopsis specularioides</td>
      <td>Great Smoky Mountains National Park</td>
      <td>85</td>
    </tr>
  </tbody>
</table>
</div>



Some scientists are studying the number of sheep sightings at different national parks.  There are several different scientific names for different types of sheep.  We'd like to know which rows of `species` are referring to sheep. 


```python
species['is_sheep']= species.common_names.apply(
lambda x: True if 'Sheep' in x else False
)
```


```python
species[species.is_sheep]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>scientific_name</th>
      <th>common_names</th>
      <th>conservation_status</th>
      <th>is_protected</th>
      <th>is_sheep</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>Mammal</td>
      <td>Ovis aries</td>
      <td>Domestic Sheep, Mouflon, Red Sheep, Sheep (Feral)</td>
      <td>No Intervention</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>1139</th>
      <td>Vascular Plant</td>
      <td>Rumex acetosella</td>
      <td>Sheep Sorrel, Sheep Sorrell</td>
      <td>No Intervention</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2233</th>
      <td>Vascular Plant</td>
      <td>Festuca filiformis</td>
      <td>Fineleaf Sheep Fescue</td>
      <td>No Intervention</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3014</th>
      <td>Mammal</td>
      <td>Ovis canadensis</td>
      <td>Bighorn Sheep, Bighorn Sheep</td>
      <td>Species of Concern</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3758</th>
      <td>Vascular Plant</td>
      <td>Rumex acetosella</td>
      <td>Common Sheep Sorrel, Field Sorrel, Red Sorrel,...</td>
      <td>No Intervention</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3761</th>
      <td>Vascular Plant</td>
      <td>Rumex paucifolius</td>
      <td>Alpine Sheep Sorrel, Fewleaved Dock, Meadow Dock</td>
      <td>No Intervention</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4091</th>
      <td>Vascular Plant</td>
      <td>Carex illota</td>
      <td>Sheep Sedge, Smallhead Sedge</td>
      <td>No Intervention</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4383</th>
      <td>Vascular Plant</td>
      <td>Potentilla ovina var. ovina</td>
      <td>Sheep Cinquefoil</td>
      <td>No Intervention</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4446</th>
      <td>Mammal</td>
      <td>Ovis canadensis sierrae</td>
      <td>Sierra Nevada Bighorn Sheep</td>
      <td>Endangered</td>
      <td>True</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



Many of the results are actually plants.  We'll select the rows of `species` where `is_sheep` is `True` and `category` is `Mammal`.


```python
sheep_species = species[(species.is_sheep == True) & (species.category == 'Mammal')]
sheep_species
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>scientific_name</th>
      <th>common_names</th>
      <th>conservation_status</th>
      <th>is_protected</th>
      <th>is_sheep</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>Mammal</td>
      <td>Ovis aries</td>
      <td>Domestic Sheep, Mouflon, Red Sheep, Sheep (Feral)</td>
      <td>No Intervention</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3014</th>
      <td>Mammal</td>
      <td>Ovis canadensis</td>
      <td>Bighorn Sheep, Bighorn Sheep</td>
      <td>Species of Concern</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4446</th>
      <td>Mammal</td>
      <td>Ovis canadensis sierrae</td>
      <td>Sierra Nevada Bighorn Sheep</td>
      <td>Endangered</td>
      <td>True</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



Now we'll merge `sheep_species` with `observations` to get a DataFrame with observations of sheep.


```python
sheep_observations = observations.merge(sheep_species)
sheep_observations
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>scientific_name</th>
      <th>park_name</th>
      <th>observations</th>
      <th>category</th>
      <th>common_names</th>
      <th>conservation_status</th>
      <th>is_protected</th>
      <th>is_sheep</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Ovis canadensis</td>
      <td>Yellowstone National Park</td>
      <td>219</td>
      <td>Mammal</td>
      <td>Bighorn Sheep, Bighorn Sheep</td>
      <td>Species of Concern</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Ovis canadensis</td>
      <td>Bryce National Park</td>
      <td>109</td>
      <td>Mammal</td>
      <td>Bighorn Sheep, Bighorn Sheep</td>
      <td>Species of Concern</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Ovis canadensis</td>
      <td>Yosemite National Park</td>
      <td>117</td>
      <td>Mammal</td>
      <td>Bighorn Sheep, Bighorn Sheep</td>
      <td>Species of Concern</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ovis canadensis</td>
      <td>Great Smoky Mountains National Park</td>
      <td>48</td>
      <td>Mammal</td>
      <td>Bighorn Sheep, Bighorn Sheep</td>
      <td>Species of Concern</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Ovis canadensis sierrae</td>
      <td>Yellowstone National Park</td>
      <td>67</td>
      <td>Mammal</td>
      <td>Sierra Nevada Bighorn Sheep</td>
      <td>Endangered</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Ovis canadensis sierrae</td>
      <td>Yosemite National Park</td>
      <td>39</td>
      <td>Mammal</td>
      <td>Sierra Nevada Bighorn Sheep</td>
      <td>Endangered</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Ovis canadensis sierrae</td>
      <td>Bryce National Park</td>
      <td>22</td>
      <td>Mammal</td>
      <td>Sierra Nevada Bighorn Sheep</td>
      <td>Endangered</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Ovis canadensis sierrae</td>
      <td>Great Smoky Mountains National Park</td>
      <td>25</td>
      <td>Mammal</td>
      <td>Sierra Nevada Bighorn Sheep</td>
      <td>Endangered</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Ovis aries</td>
      <td>Yosemite National Park</td>
      <td>126</td>
      <td>Mammal</td>
      <td>Domestic Sheep, Mouflon, Red Sheep, Sheep (Feral)</td>
      <td>No Intervention</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Ovis aries</td>
      <td>Great Smoky Mountains National Park</td>
      <td>76</td>
      <td>Mammal</td>
      <td>Domestic Sheep, Mouflon, Red Sheep, Sheep (Feral)</td>
      <td>No Intervention</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Ovis aries</td>
      <td>Bryce National Park</td>
      <td>119</td>
      <td>Mammal</td>
      <td>Domestic Sheep, Mouflon, Red Sheep, Sheep (Feral)</td>
      <td>No Intervention</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Ovis aries</td>
      <td>Yellowstone National Park</td>
      <td>221</td>
      <td>Mammal</td>
      <td>Domestic Sheep, Mouflon, Red Sheep, Sheep (Feral)</td>
      <td>No Intervention</td>
      <td>False</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



How many total sheep observations (across all three species) were made at each national park?

This is the total number of sheep observed in each park over the past 7 days.


```python
obs_by_park = sheep_observations.groupby('park_name').observations.sum().reset_index()
obs_by_park
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>park_name</th>
      <th>observations</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bryce National Park</td>
      <td>250</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Great Smoky Mountains National Park</td>
      <td>149</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Yellowstone National Park</td>
      <td>507</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Yosemite National Park</td>
      <td>282</td>
    </tr>
  </tbody>
</table>
</div>



Bar chart time


```python
plt.figure(figsize=(16, 4))
ax = plt.subplot()
plt.bar(range(len(obs_by_park)),
        obs_by_park.observations.values)
ax.set_xticks(range(len(obs_by_park)))
ax.set_xticklabels(obs_by_park.park_name.values)
plt.ylabel('Number of Observations')
plt.title('Observations of Sheep per Week')
plt.show()
plt.savefig('Observations of sheep per week.png')
```


    
![png](output_51_0.png)
    



    <Figure size 432x288 with 0 Axes>


The scientists know that 15% of sheep at Bryce National Park have foot and mouth disease.  Park rangers at Yellowstone National Park have been running a program to reduce the rate of foot and mouth disease at that park.  The scientists want to test whether or not this program is working.  They want to be able to detect reductions of at least 5 percentage points.  For instance, if 10% of sheep in Yellowstone have foot and mouth disease, they'd like to be able to know this, with confidence.

Using a sample size calculator, we want to calculate the number of sheep that they would need to observe from each park.  We'll use the default level of significance (90%).

First we need to calculate the minimum detectable effect


```python
minimum_detectable_effect = 100 * 0.05 / 0.15
minimum_detectable_effect
```




    33.333333333333336




```python
baseline = 15
```


```python
sample_size_per_variant = 870
```

How many weeks would you need to observe sheep at Bryce National Park in order to observe enough sheep?  How many weeks would you need to observe at Yellowstone National Park to observe enough sheep?


```python
bryce = 870 / 250.
yellowstone = 810 / 507.

# Approximately 3.5 weeks at Bryce and 1.5 weeks at Yellowstone.
```
