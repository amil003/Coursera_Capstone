### To start, I chose to use pandas to scrape from Wikipedia but was having a load of difficulty with lxml. Tried to install with pip to no avail so had to use conda instead.


```python
!conda install -c conda-forge lxml --yes

from lxml import etree
print('lxml imported!')
```

    Collecting package metadata (current_repodata.json): done
    Solving environment: done
    
    # All requested packages already installed.
    
    lxml imported!



```python
import pandas as pd
```

### Reading the dataset from the url.


```python
toronto_df=pd.read_html('https://en.wikipedia.org/wiki/List_of_postal_codes_of_Canada:_M')[0]
```


```python
toronto_df.head()
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
      <th>Postal Code</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M2A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park, Harbourfront</td>
    </tr>
  </tbody>
</table>
</div>



### 3B. Now to drop those boroughs which are classified as not assigned.


```python
toronto_df_drop = toronto_df[toronto_df.Borough != "Not assigned"].reset_index(drop=True)
toronto_df_drop.head()
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
      <th>Postal Code</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park, Harbourfront</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor, Lawrence Heights</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M7A</td>
      <td>Downtown Toronto</td>
      <td>Queen's Park, Ontario Provincial Government</td>
    </tr>
  </tbody>
</table>
</div>



### 3D. Change the neighbourhoods classified as not assigned to match their borough assignment.


```python
for index, row in toronto_df_drop.iterrows():
    if row["Neighbourhood"] == "Not assigned":
        row["Neighbourhood"] = row["Borough"]
        
toronto_df_drop.head()
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
      <th>Postal Code</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park, Harbourfront</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor, Lawrence Heights</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M7A</td>
      <td>Downtown Toronto</td>
      <td>Queen's Park, Ontario Provincial Government</td>
    </tr>
  </tbody>
</table>
</div>



### Checking our work thus far to make sure it matches the given table.


```python
column_names = ["Postal Code", "Borough", "Neighbourhood"]
test_df = pd.DataFrame(columns=column_names)

test_list = ["M5G", "M2H", "M4B", "M1J", "M4G", "M4M", "M1R", "M9V", "M9L", "M5V", "M1B", "M5A"]

for postcode in test_list:
    test_df = test_df.append(toronto_df_drop[toronto_df_drop["Postal Code"]==postcode], ignore_index=True)
    
test_df
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
      <th>Postal Code</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M5G</td>
      <td>Downtown Toronto</td>
      <td>Central Bay Street</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M2H</td>
      <td>North York</td>
      <td>Hillcrest Village</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Parkview Hill, Woodbine Gardens</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M1J</td>
      <td>Scarborough</td>
      <td>Scarborough Village</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M4G</td>
      <td>East York</td>
      <td>Leaside</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M4M</td>
      <td>East Toronto</td>
      <td>Studio District</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M1R</td>
      <td>Scarborough</td>
      <td>Wexford, Maryvale</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M9V</td>
      <td>Etobicoke</td>
      <td>South Steeles, Silverstone, Humbergate, Jamest...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M9L</td>
      <td>North York</td>
      <td>Humber Summit</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M5V</td>
      <td>Downtown Toronto</td>
      <td>CN Tower, King and Spadina, Railway Lands, Har...</td>
    </tr>
    <tr>
      <th>10</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern, Rouge</td>
    </tr>
    <tr>
      <th>11</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park, Harbourfront</td>
    </tr>
  </tbody>
</table>
</div>



### Finally, checking the shape of our dataframe.


```python
toronto_df_drop.shape
```




    (103, 3)


