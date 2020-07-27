# Assignment
# Segmenting and Clustering Neighborhoods in Toronto | Part-2

1. Start by creating a new Notebook for this assignment.

2. Use the Notebook to build the code to scrape the following Wikipedia page, https://en.wikipedia.org/wiki/List_of_postal_codes_of_Canada:_M, in order to obtain the data that is in the table of postal codes and to transform the data into a pandas dataframe.
For this assignment, you will be required to explore and cluster the neighborhoods in Toronto.

3. To create the above dataframe:

. 
- The dataframe will consist of three columns: PostalCode, Borough, and Neighborhood.
- Only process the cells that have an assigned borough. Ignore cells with a borough that is Not assigned.
- More than one neighborhood can exist in one postal code area. For example, in the table on the Wikipedia page, you will notice that M5A is listed twice and has two neighborhoods: Harbourfront and Regent Park. These two rows will be combined into one row with the neighborhoods separated with a comma as shown in row 11 in the above table.
- If a cell has a borough but a Not assigned neighborhood, then the neighborhood will be the same as the borough. So for the 9th cell in the table on the Wikipedia page, the value of the Borough and the Neighborhood columns will be Queen's Park.
- Clean your Notebook and add Markdown cells to explain your work and any assumptions you are making.
- In the last cell of your notebook, use the .shape method to print the number of rows of your dataframe.
.


4. Submit a link to your Notebook on your Github repository. (10 marks)

Note: There are different website scraping libraries and packages in Python. For scraping the above table, you can simply use pandas to read the table into a pandas dataframe.

Another way, which would help to learn for more complicated cases of web scraping is using the BeautifulSoup package. Here is the package's main documentation page: http://beautiful-soup-4.readthedocs.io/en/latest/

The package is so popular that there is a plethora of tutorials and examples on how to use it. Here is a very good Youtube video on how to use the BeautifulSoup package: https://www.youtube.com/watch?v=ng2o98k983k

Use pandas, or the BeautifulSoup package, or any other way you are comfortable with to transform the data in the table on the Wikipedia page into the above pandas dataframe.

#### Adding Latitude and Longitude Co-ordinates to the DataFrame

Installing Library


```python
!pip install geocoder
```

    Requirement already satisfied: geocoder in c:\programdata\anaconda3\lib\site-packages (1.38.1)
    Requirement already satisfied: ratelim in c:\programdata\anaconda3\lib\site-packages (from geocoder) (0.1.6)
    Requirement already satisfied: six in c:\users\anonymous\appdata\roaming\python\python37\site-packages (from geocoder) (1.12.0)
    Requirement already satisfied: requests in c:\programdata\anaconda3\lib\site-packages (from geocoder) (2.21.0)
    Requirement already satisfied: future in c:\programdata\anaconda3\lib\site-packages (from geocoder) (0.17.1)
    Requirement already satisfied: click in c:\programdata\anaconda3\lib\site-packages (from geocoder) (7.0)
    Requirement already satisfied: decorator in c:\programdata\anaconda3\lib\site-packages (from ratelim->geocoder) (4.3.0)
    Requirement already satisfied: chardet<3.1.0,>=3.0.2 in c:\programdata\anaconda3\lib\site-packages (from requests->geocoder) (3.0.4)
    Requirement already satisfied: idna<2.9,>=2.5 in c:\programdata\anaconda3\lib\site-packages (from requests->geocoder) (2.8)
    Requirement already satisfied: urllib3<1.25,>=1.21.1 in c:\programdata\anaconda3\lib\site-packages (from requests->geocoder) (1.24.1)
    Requirement already satisfied: certifi>=2017.4.17 in c:\programdata\anaconda3\lib\site-packages (from requests->geocoder) (2018.11.29)


Importing Libraries


```python
import pandas as pd
import numpy as np
import geocoder
print("Imported!")
```

    Imported!


Reading the Toronto.csv which created on Part 1 Notebook


```python
df = pd.read_csv('toronto.csv')
df.head()
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
      <th>Postalcode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Rouge, Malvern</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Highland Creek, Rouge Hill, Port Union</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood, Morningside, West Hill</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
    </tr>
  </tbody>
</table>
</div>




```python
print(df.shape)
df.describe()
```

    (103, 3)





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
      <th>Postalcode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>103</td>
      <td>103</td>
      <td>103</td>
    </tr>
    <tr>
      <th>unique</th>
      <td>103</td>
      <td>11</td>
      <td>103</td>
    </tr>
    <tr>
      <th>top</th>
      <td>M4K</td>
      <td>North York</td>
      <td>Willowdale South</td>
    </tr>
    <tr>
      <th>freq</th>
      <td>1</td>
      <td>24</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
def get_latilong(postal_code):
    lati_long_coords = None
    while(lati_long_coords is None):
        g = geocoder.arcgis('{}, Toronto, Ontario'.format(postal_code))
        lati_long_coords = g.latlng
    return lati_long_coords
    
get_latilong('M4G')
```




    [43.70949500000006, -79.36398897099997]




```python
# Retrieving Postal Code Co-ordinates
postal_codes = df['Postalcode']    
coords = [ get_latilong(postal_code) for postal_code in postal_codes.tolist() ]
```


```python
# Adding Columns Latitude & Longitude
df_coords = pd.DataFrame(coords, columns=['Latitude', 'Longitude'])
df['Latitude'] = df_coords['Latitude']
df['Longitude'] = df_coords['Longitude']
```


```python
df[df.Postalcode == 'M5G']
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
      <th>Postalcode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>57</th>
      <td>M5G</td>
      <td>Downtown Toronto</td>
      <td>Central Bay Street</td>
      <td>43.656091</td>
      <td>-79.38493</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.head(15)
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
      <th>Postalcode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Rouge, Malvern</td>
      <td>43.811525</td>
      <td>-79.195517</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Highland Creek, Rouge Hill, Port Union</td>
      <td>43.785665</td>
      <td>-79.158725</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood, Morningside, West Hill</td>
      <td>43.765815</td>
      <td>-79.175193</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
      <td>43.768369</td>
      <td>-79.217590</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
      <td>43.769688</td>
      <td>-79.239440</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M1J</td>
      <td>Scarborough</td>
      <td>Scarborough Village</td>
      <td>43.743125</td>
      <td>-79.231750</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M1K</td>
      <td>Scarborough</td>
      <td>East Birchmount Park, Ionview, Kennedy Park</td>
      <td>43.726276</td>
      <td>-79.263625</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M1L</td>
      <td>Scarborough</td>
      <td>Clairlea, Golden Mile, Oakridge</td>
      <td>43.713054</td>
      <td>-79.285055</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M1M</td>
      <td>Scarborough</td>
      <td>Cliffcrest, Cliffside, Scarborough Village West</td>
      <td>43.724235</td>
      <td>-79.227925</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M1N</td>
      <td>Scarborough</td>
      <td>Birch Cliff, Cliffside West</td>
      <td>43.696770</td>
      <td>-79.259967</td>
    </tr>
    <tr>
      <th>10</th>
      <td>M1P</td>
      <td>Scarborough</td>
      <td>Dorset Park, Scarborough Town Centre, Wexford ...</td>
      <td>43.759975</td>
      <td>-79.268974</td>
    </tr>
    <tr>
      <th>11</th>
      <td>M1R</td>
      <td>Scarborough</td>
      <td>Maryvale, Wexford</td>
      <td>43.750710</td>
      <td>-79.300560</td>
    </tr>
    <tr>
      <th>12</th>
      <td>M1S</td>
      <td>Scarborough</td>
      <td>Agincourt</td>
      <td>43.793940</td>
      <td>-79.267976</td>
    </tr>
    <tr>
      <th>13</th>
      <td>M1T</td>
      <td>Scarborough</td>
      <td>Clarks Corners, Sullivan, Tam O'Shanter</td>
      <td>43.784725</td>
      <td>-79.299066</td>
    </tr>
    <tr>
      <th>14</th>
      <td>M1V</td>
      <td>Scarborough</td>
      <td>Agincourt North, L'Amoreaux East, Milliken, St...</td>
      <td>43.817685</td>
      <td>-79.280187</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.to_csv('toronto_part2.csv',index=False)
```


```python

```
