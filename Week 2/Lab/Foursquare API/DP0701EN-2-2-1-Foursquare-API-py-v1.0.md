<a href="https://cognitiveclass.ai"><img src = "https://ibm.box.com/shared/static/9gegpsmnsoo25ikkbl4qzlvlyjbgxs5x.png" width = 400> </a>

<h1 align=center><font size = 5>Learning FourSquare API with Python</font></h1>

   

## Introduction

In this lab, you will learn in details how to make calls to the Foursquare API for different purposes. You will learn how to construct a URL to send a request to the API to search for a specific type of venues, to explore a particular venue, to explore a Foursquare user, to explore a geographical location, and to get trending venues around a location. Also, you will learn how to use the visualization library, Folium, to visualize the results.

## Table of Contents

1. <a href="#item1">Foursquare API Search Function</a>
2. <a href="#item2">Explore a Given Venue</a>  
3. <a href="#item3">Explore a User</a>  
4. <a href="#item4">Foursquare API Explore Function</a>  
5. <a href="#item5">Get Trending Venues</a>  

### Import necessary Libraries


```python
import requests # library to handle requests
import pandas as pd # library for data analsysis
import numpy as np # library to handle data in a vectorized manner
import random # library for random number generation

!conda install -c conda-forge geopy --yes 
from geopy.geocoders import Nominatim # module to convert an address into latitude and longitude values

# libraries for displaying images
from IPython.display import Image 
from IPython.core.display import HTML 
    
# tranforming json file into a pandas dataframe library
from pandas.io.json import json_normalize

!conda install -c conda-forge folium=0.5.0 --yes
import folium # plotting library

print('Folium installed')
print('Libraries imported.')
```

    Collecting package metadata (current_repodata.json): done
    Solving environment: done
    
    # All requested packages already installed.
    
    Collecting package metadata (current_repodata.json): done
    Solving environment: done
    
    # All requested packages already installed.
    
    Folium installed
    Libraries imported.


### Define Foursquare Credentials and Version

##### Make sure that you have created a Foursquare developer account and have your credentials handy


```python
CLIENT_ID = '1D4LUFF5Y0G2E2M52RW2QRFJLDT5CJH1QJUN0ANQLIJJCPU3' # 'your-client-ID' # your Foursquare ID
CLIENT_SECRET = '05X3YLFANXLSHZ0J3RLUWTY3QGZ0D2ZCJQGSQDU5QBCCUEKL' # 'your-client-secret' # your Foursquare Secret
VERSION = '20180604'
LIMIT = 30
print('Your credentails:')
print('CLIENT_ID: ' + CLIENT_ID)
print('CLIENT_SECRET:' + CLIENT_SECRET)
```

    Your credentails:
    CLIENT_ID: 1D4LUFF5Y0G2E2M52RW2QRFJLDT5CJH1QJUN0ANQLIJJCPU3
    CLIENT_SECRET:05X3YLFANXLSHZ0J3RLUWTY3QGZ0D2ZCJQGSQDU5QBCCUEKL


  

#### Let's again assume that you are staying at the Conrad hotel. So let's start by converting the Contrad Hotel's address to its latitude and longitude coordinates.

In order to define an instance of the geocoder, we need to define a user_agent. We will name our agent <em>foursquare_agent</em>, as shown below.


```python
address = '102 North End Ave, New York, NY'

geolocator = Nominatim(user_agent="foursquare_agent")
location = geolocator.geocode(address)
latitude = location.latitude
longitude = location.longitude
print(latitude, longitude)
```

    40.7151482 -74.0156573


   

<a id="item1"></a>

## 1. Search for a specific venue category
> `https://api.foursquare.com/v2/venues/`**search**`?client_id=`**CLIENT_ID**`&client_secret=`**CLIENT_SECRET**`&ll=`**LATITUDE**`,`**LONGITUDE**`&v=`**VERSION**`&query=`**QUERY**`&radius=`**RADIUS**`&limit=`**LIMIT**

#### Now, let's assume that it is lunch time, and you are craving Italian food. So, let's define a query to search for Italian food that is within 500 metres from the Conrad Hotel. 


```python
search_query = 'Italian'
radius = 500
print(search_query + ' .... OK!')
```

    Italian .... OK!


#### Define the corresponding URL


```python
url = 'https://api.foursquare.com/v2/venues/search?client_id={}&client_secret={}&ll={},{}&v={}&query={}&radius={}&limit={}'.format(CLIENT_ID, CLIENT_SECRET, latitude, longitude, VERSION, search_query, radius, LIMIT)
url
```




    'https://api.foursquare.com/v2/venues/search?client_id=1D4LUFF5Y0G2E2M52RW2QRFJLDT5CJH1QJUN0ANQLIJJCPU3&client_secret=05X3YLFANXLSHZ0J3RLUWTY3QGZ0D2ZCJQGSQDU5QBCCUEKL&ll=40.7151482,-74.0156573&v=20180604&query=Italian&radius=500&limit=30'



#### Send the GET Request and examine the results


```python
results = requests.get(url).json()
results
```




    {'meta': {'code': 200, 'requestId': '5f1ec4773d22411a21ed200d'},
     'response': {'venues': [{'id': '4fa862b3e4b0ebff2f749f06',
        'name': "Harry's Italian Pizza Bar",
        'location': {'address': '225 Murray St',
         'lat': 40.71521779064671,
         'lng': -74.01473940209351,
         'labeledLatLngs': [{'label': 'display',
           'lat': 40.71521779064671,
           'lng': -74.01473940209351},
          {'label': 'entrance', 'lat': 40.715361, 'lng': -74.014975}],
         'distance': 77,
         'postalCode': '10282',
         'cc': 'US',
         'city': 'New York',
         'state': 'NY',
         'country': 'United States',
         'formattedAddress': ['225 Murray St',
          'New York, NY 10282',
          'United States']},
        'categories': [{'id': '4bf58dd8d48988d1ca941735',
          'name': 'Pizza Place',
          'pluralName': 'Pizza Places',
          'shortName': 'Pizza',
          'icon': {'prefix': 'https://ss3.4sqi.net/img/categories_v2/food/pizza_',
           'suffix': '.png'},
          'primary': True}],
        'referralId': 'v-1595852451',
        'hasPerk': False},
       {'id': '4f3232e219836c91c7bfde94',
        'name': 'Conca Cucina Italian Restaurant',
        'location': {'address': '63 W Broadway',
         'lat': 40.714484000000006,
         'lng': -74.00980600000001,
         'labeledLatLngs': [{'label': 'display',
           'lat': 40.714484000000006,
           'lng': -74.00980600000001}],
         'distance': 499,
         'postalCode': '10007',
         'cc': 'US',
         'city': 'New York',
         'state': 'NY',
         'country': 'United States',
         'formattedAddress': ['63 W Broadway',
          'New York, NY 10007',
          'United States']},
        'categories': [{'id': '4d4b7105d754a06374d81259',
          'name': 'Food',
          'pluralName': 'Food',
          'shortName': 'Food',
          'icon': {'prefix': 'https://ss3.4sqi.net/img/categories_v2/food/default_',
           'suffix': '.png'},
          'primary': True}],
        'referralId': 'v-1595852451',
        'hasPerk': False}]}}



#### Get relevant part of JSON and transform it into a *pandas* dataframe


```python
# assign relevant part of JSON to venues
venues = results['response']['venues']

# tranform venues into a dataframe
# dataframe = json_normalize(venues)
dataframe = pd.json_normalize(venues)
dataframe.head()
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
      <th>id</th>
      <th>name</th>
      <th>categories</th>
      <th>referralId</th>
      <th>hasPerk</th>
      <th>location.address</th>
      <th>location.lat</th>
      <th>location.lng</th>
      <th>location.labeledLatLngs</th>
      <th>location.distance</th>
      <th>location.postalCode</th>
      <th>location.cc</th>
      <th>location.city</th>
      <th>location.state</th>
      <th>location.country</th>
      <th>location.formattedAddress</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4fa862b3e4b0ebff2f749f06</td>
      <td>Harry's Italian Pizza Bar</td>
      <td>[{'id': '4bf58dd8d48988d1ca941735', 'name': 'P...</td>
      <td>v-1595852451</td>
      <td>False</td>
      <td>225 Murray St</td>
      <td>40.715218</td>
      <td>-74.014739</td>
      <td>[{'label': 'display', 'lat': 40.71521779064671...</td>
      <td>77</td>
      <td>10282</td>
      <td>US</td>
      <td>New York</td>
      <td>NY</td>
      <td>United States</td>
      <td>[225 Murray St, New York, NY 10282, United Sta...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4f3232e219836c91c7bfde94</td>
      <td>Conca Cucina Italian Restaurant</td>
      <td>[{'id': '4d4b7105d754a06374d81259', 'name': 'F...</td>
      <td>v-1595852451</td>
      <td>False</td>
      <td>63 W Broadway</td>
      <td>40.714484</td>
      <td>-74.009806</td>
      <td>[{'label': 'display', 'lat': 40.71448400000000...</td>
      <td>499</td>
      <td>10007</td>
      <td>US</td>
      <td>New York</td>
      <td>NY</td>
      <td>United States</td>
      <td>[63 W Broadway, New York, NY 10007, United Sta...</td>
    </tr>
  </tbody>
</table>
</div>



#### Define information of interest and filter dataframe


```python
# keep only columns that include venue name, and anything that is associated with location
filtered_columns = ['name', 'categories'] + [col for col in dataframe.columns if col.startswith('location.')] + ['id']
dataframe_filtered = dataframe.loc[:, filtered_columns]

# function that extracts the category of the venue
def get_category_type(row):
    try:
        categories_list = row['categories']
    except:
        categories_list = row['venue.categories']
        
    if len(categories_list) == 0:
        return None
    else:
        return categories_list[0]['name']

# filter the category for each row
dataframe_filtered['categories'] = dataframe_filtered.apply(get_category_type, axis=1)

# clean column names by keeping only last term
dataframe_filtered.columns = [column.split('.')[-1] for column in dataframe_filtered.columns]

dataframe_filtered
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
      <th>name</th>
      <th>categories</th>
      <th>address</th>
      <th>lat</th>
      <th>lng</th>
      <th>labeledLatLngs</th>
      <th>distance</th>
      <th>postalCode</th>
      <th>cc</th>
      <th>city</th>
      <th>state</th>
      <th>country</th>
      <th>formattedAddress</th>
      <th>id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Harry's Italian Pizza Bar</td>
      <td>Pizza Place</td>
      <td>225 Murray St</td>
      <td>40.715218</td>
      <td>-74.014739</td>
      <td>[{'label': 'display', 'lat': 40.71521779064671...</td>
      <td>77</td>
      <td>10282</td>
      <td>US</td>
      <td>New York</td>
      <td>NY</td>
      <td>United States</td>
      <td>[225 Murray St, New York, NY 10282, United Sta...</td>
      <td>4fa862b3e4b0ebff2f749f06</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Conca Cucina Italian Restaurant</td>
      <td>Food</td>
      <td>63 W Broadway</td>
      <td>40.714484</td>
      <td>-74.009806</td>
      <td>[{'label': 'display', 'lat': 40.71448400000000...</td>
      <td>499</td>
      <td>10007</td>
      <td>US</td>
      <td>New York</td>
      <td>NY</td>
      <td>United States</td>
      <td>[63 W Broadway, New York, NY 10007, United Sta...</td>
      <td>4f3232e219836c91c7bfde94</td>
    </tr>
  </tbody>
</table>
</div>



#### Let's visualize the Italian restaurants that are nearby


```python
dataframe_filtered.name
```




    0          Harry's Italian Pizza Bar
    1    Conca Cucina Italian Restaurant
    Name: name, dtype: object




```python
venues_map = folium.Map(location=[latitude, longitude], zoom_start=13) # generate map centred around the Conrad Hotel

# add a red circle marker to represent the Conrad Hotel
folium.features.CircleMarker(
    [latitude, longitude],
    radius=10,
    color='red',
    popup='Conrad Hotel',
    fill = True,
    fill_color = 'red',
    fill_opacity = 0.6
).add_to(venues_map)

# add the Italian restaurants as blue circle markers
for lat, lng, label in zip(dataframe_filtered.lat, dataframe_filtered.lng, dataframe_filtered.categories):
    folium.features.CircleMarker(
        [lat, lng],
        radius=5,
        color='blue',
        popup=label,
        fill = True,
        fill_color='blue',
        fill_opacity=0.6
    ).add_to(venues_map)

# display map
venues_map
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe src="about:blank" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" data-html=PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfZjQ2MGZhY2RkOTk0NGRhMjkxNzY1YzM5NWQ0NmNjZTAgewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwX2Y0NjBmYWNkZDk5NDRkYTI5MTc2NWMzOTVkNDZjY2UwIiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF9mNDYwZmFjZGQ5OTQ0ZGEyOTE3NjVjMzk1ZDQ2Y2NlMCA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF9mNDYwZmFjZGQ5OTQ0ZGEyOTE3NjVjMzk1ZDQ2Y2NlMCcsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNDAuNzE1MTQ4MiwtNzQuMDE1NjU3M10sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB6b29tOiAxMywKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIG1heEJvdW5kczogYm91bmRzLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgbGF5ZXJzOiBbXSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHdvcmxkQ29weUp1bXA6IGZhbHNlLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgY3JzOiBMLkNSUy5FUFNHMzg1NwogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHRpbGVfbGF5ZXJfZmQyNWRiZmY2MmNhNDA5Y2IxOGMxMDI4MjA5NmM1NzcgPSBMLnRpbGVMYXllcigKICAgICAgICAgICAgICAgICdodHRwczovL3tzfS50aWxlLm9wZW5zdHJlZXRtYXAub3JnL3t6fS97eH0ve3l9LnBuZycsCiAgICAgICAgICAgICAgICB7CiAgImF0dHJpYnV0aW9uIjogbnVsbCwKICAiZGV0ZWN0UmV0aW5hIjogZmFsc2UsCiAgIm1heFpvb20iOiAxOCwKICAibWluWm9vbSI6IDEsCiAgIm5vV3JhcCI6IGZhbHNlLAogICJzdWJkb21haW5zIjogImFiYyIKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZjQ2MGZhY2RkOTk0NGRhMjkxNzY1YzM5NWQ0NmNjZTApOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzk2NWVhOTQ2OGMzNzRmNzk5NDZlNmQwNWYwYThhMjBjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuNzE1MTQ4MiwtNzQuMDE1NjU3M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJyZWQiLAogICJmaWxsT3BhY2l0eSI6IDAuNiwKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDEwLAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2Y0NjBmYWNkZDk5NDRkYTI5MTc2NWMzOTVkNDZjY2UwKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzljMWZmMmIzZTliMDRmYWI4OTA4MjliZmRlNDE0ZmNmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzM5ZmUyMjVhODU2YTQ0MzhiNzliNTk2YWFmYmFmY2NmID0gJCgnPGRpdiBpZD0iaHRtbF8zOWZlMjI1YTg1NmE0NDM4Yjc5YjU5NmFhZmJhZmNjZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q29ucmFkIEhvdGVsPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85YzFmZjJiM2U5YjA0ZmFiODkwODI5YmZkZTQxNGZjZi5zZXRDb250ZW50KGh0bWxfMzlmZTIyNWE4NTZhNDQzOGI3OWI1OTZhYWZiYWZjY2YpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOTY1ZWE5NDY4YzM3NGY3OTk0NmU2ZDA1ZjBhOGEyMGMuYmluZFBvcHVwKHBvcHVwXzljMWZmMmIzZTliMDRmYWI4OTA4MjliZmRlNDE0ZmNmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzNjNDE3Mzc4Mzk0YjQzYzhhZWRkMzY1ZmQzZGZlYjRjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuNzE1MjE3NzkwNjQ2NzEsLTc0LjAxNDczOTQwMjA5MzUxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjYsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2Y0NjBmYWNkZDk5NDRkYTI5MTc2NWMzOTVkNDZjY2UwKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzQ3MTZhNWNiOWJlNjQzMDQ5NWMxZTFlOTVhYjFjNzBmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzNhNzFlNDZiNTBhODRlYzM4NGQ0MGZlMmIyZThlMGU1ID0gJCgnPGRpdiBpZD0iaHRtbF8zYTcxZTQ2YjUwYTg0ZWMzODRkNDBmZTJiMmU4ZTBlNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UGl6emEgUGxhY2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQ3MTZhNWNiOWJlNjQzMDQ5NWMxZTFlOTVhYjFjNzBmLnNldENvbnRlbnQoaHRtbF8zYTcxZTQ2YjUwYTg0ZWMzODRkNDBmZTJiMmU4ZTBlNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zYzQxNzM3ODM5NGI0M2M4YWVkZDM2NWZkM2RmZWI0Yy5iaW5kUG9wdXAocG9wdXBfNDcxNmE1Y2I5YmU2NDMwNDk1YzFlMWU5NWFiMWM3MGYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzZhYjUxZDk3NGEyNDM4ZWE4NDM4ZDcyMDcyYzJmZDkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0MC43MTQ0ODQwMDAwMDAwMDYsLTc0LjAwOTgwNjAwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjYsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2Y0NjBmYWNkZDk5NDRkYTI5MTc2NWMzOTVkNDZjY2UwKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2E1MWQ5NjhlM2ViYzQ4NmE4NGY2MTVkZTAyZjk1OWJhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzM2YzFiZGQ1NzI1NTRkNWRhOTM0OTQ2MWZiYzdmOGUxID0gJCgnPGRpdiBpZD0iaHRtbF8zNmMxYmRkNTcyNTU0ZDVkYTkzNDk0NjFmYmM3ZjhlMSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Rm9vZDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYTUxZDk2OGUzZWJjNDg2YTg0ZjYxNWRlMDJmOTU5YmEuc2V0Q29udGVudChodG1sXzM2YzFiZGQ1NzI1NTRkNWRhOTM0OTQ2MWZiYzdmOGUxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzM2YWI1MWQ5NzRhMjQzOGVhODQzOGQ3MjA3MmMyZmQ5LmJpbmRQb3B1cChwb3B1cF9hNTFkOTY4ZTNlYmM0ODZhODRmNjE1ZGUwMmY5NTliYSk7CgogICAgICAgICAgICAKICAgICAgICAKPC9zY3JpcHQ+ onload="this.contentDocument.open();this.contentDocument.write(atob(this.getAttribute('data-html')));this.contentDocument.close();" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



   

<a id="item2"></a>

## 2. Explore a Given Venue
> `https://api.foursquare.com/v2/venues/`**VENUE_ID**`?client_id=`**CLIENT_ID**`&client_secret=`**CLIENT_SECRET**`&v=`**VERSION**

### A. Let's explore the closest Italian restaurant -- _Harry's Italian Pizza Bar_


```python
venue_id = '4fa862b3e4b0ebff2f749f06' # ID of Harry's Italian Pizza Bar
url = 'https://api.foursquare.com/v2/venues/{}?client_id={}&client_secret={}&v={}'.format(venue_id, CLIENT_ID, CLIENT_SECRET, VERSION)
url
```




    'https://api.foursquare.com/v2/venues/4fa862b3e4b0ebff2f749f06?client_id=1D4LUFF5Y0G2E2M52RW2QRFJLDT5CJH1QJUN0ANQLIJJCPU3&client_secret=05X3YLFANXLSHZ0J3RLUWTY3QGZ0D2ZCJQGSQDU5QBCCUEKL&v=20180604'



#### Send GET request for result


```python
result = requests.get(url).json()
print(result['response']['venue'].keys())
result['response']['venue']
```

    dict_keys(['id', 'name', 'contact', 'location', 'canonicalUrl', 'categories', 'verified', 'stats', 'url', 'price', 'hasMenu', 'likes', 'dislike', 'ok', 'rating', 'ratingColor', 'ratingSignals', 'menu', 'allowMenuUrlEdit', 'beenHere', 'specials', 'photos', 'reasons', 'hereNow', 'createdAt', 'tips', 'shortUrl', 'timeZone', 'listed', 'hours', 'popular', 'seasonalHours', 'defaultHours', 'pageUpdates', 'inbox', 'attributes', 'bestPhoto', 'colors'])





    {'id': '4fa862b3e4b0ebff2f749f06',
     'name': "Harry's Italian Pizza Bar",
     'contact': {'phone': '2126081007', 'formattedPhone': '(212) 608-1007'},
     'location': {'address': '225 Murray St',
      'lat': 40.71521779064671,
      'lng': -74.01473940209351,
      'labeledLatLngs': [{'label': 'display',
        'lat': 40.71521779064671,
        'lng': -74.01473940209351},
       {'label': 'entrance', 'lat': 40.715361, 'lng': -74.014975}],
      'postalCode': '10282',
      'cc': 'US',
      'city': 'New York',
      'state': 'NY',
      'country': 'United States',
      'formattedAddress': ['225 Murray St',
       'New York, NY 10282',
       'United States']},
     'canonicalUrl': 'https://foursquare.com/v/harrys-italian-pizza-bar/4fa862b3e4b0ebff2f749f06',
     'categories': [{'id': '4bf58dd8d48988d1ca941735',
       'name': 'Pizza Place',
       'pluralName': 'Pizza Places',
       'shortName': 'Pizza',
       'icon': {'prefix': 'https://ss3.4sqi.net/img/categories_v2/food/pizza_',
        'suffix': '.png'},
       'primary': True},
      {'id': '4bf58dd8d48988d110941735',
       'name': 'Italian Restaurant',
       'pluralName': 'Italian Restaurants',
       'shortName': 'Italian',
       'icon': {'prefix': 'https://ss3.4sqi.net/img/categories_v2/food/italian_',
        'suffix': '.png'}}],
     'verified': False,
     'stats': {'tipCount': 57},
     'url': 'http://harrysitalian.com',
     'price': {'tier': 2, 'message': 'Moderate', 'currency': '$'},
     'hasMenu': True,
     'likes': {'count': 120,
      'groups': [{'type': 'others', 'count': 120, 'items': []}],
      'summary': '120 Likes'},
     'dislike': False,
     'ok': False,
     'rating': 6.9,
     'ratingColor': 'FFC800',
     'ratingSignals': 212,
     'menu': {'type': 'Menu',
      'label': 'Menu',
      'anchor': 'View Menu',
      'url': 'https://foursquare.com/v/harrys-italian-pizza-bar/4fa862b3e4b0ebff2f749f06/menu',
      'mobileUrl': 'https://foursquare.com/v/4fa862b3e4b0ebff2f749f06/device_menu'},
     'allowMenuUrlEdit': True,
     'beenHere': {'count': 0,
      'unconfirmedCount': 0,
      'marked': False,
      'lastCheckinExpiredAt': 0},
     'specials': {'count': 0, 'items': []},
     'photos': {'count': 146,
      'groups': [{'type': 'venue',
        'name': 'Venue photos',
        'count': 146,
        'items': [{'id': '4fad980de4b091b4626c3633',
          'createdAt': 1336776717,
          'source': {'name': 'Foursquare for Android',
           'url': 'https://foursquare.com/download/#/android'},
          'prefix': 'https://fastly.4sqi.net/img/general/',
          'suffix': '/ya1iQFI7pLjuIJp1PGDKlrZS3OJdHCF7tpILMmjv_2w.jpg',
          'width': 480,
          'height': 640,
          'user': {'id': '13676709',
           'firstName': 'Leony',
           'lastName': 'N',
           'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
            'suffix': '/T0ANFNGNMCHUDEUE.jpg'}},
          'visibility': 'public'}]}]},
     'reasons': {'count': 1,
      'items': [{'summary': 'Lots of people like this place',
        'type': 'general',
        'reasonName': 'rawLikesReason'}]},
     'hereNow': {'count': 0, 'summary': 'Nobody here', 'groups': []},
     'createdAt': 1336435379,
     'tips': {'count': 57,
      'groups': [{'type': 'others',
        'name': 'All tips',
        'count': 57,
        'items': [{'id': '53d27909498e0523841340b6',
          'createdAt': 1406302473,
          'text': "Harry's Italian Pizza bar is known for it's amazing pizza, but did you know that the brunches here are amazing too? Try the Nutella French toast and we know you'll be sold.",
          'type': 'user',
          'canonicalUrl': 'https://foursquare.com/item/53d27909498e0523841340b6',
          'lang': 'en',
          'likes': {'count': 4,
           'groups': [{'type': 'others',
             'count': 4,
             'items': [{'id': '369426',
               'firstName': 'P.',
               'lastName': 'M',
               'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
                'suffix': '/JPQYUWJKUT0H2OO4.jpg'}},
              {'id': '87587879',
               'firstName': 'Diane',
               'lastName': 'D',
               'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
                'suffix': '/87587879-ESLRSZLQ2CBE2P4W.jpg'}},
              {'id': '87591341',
               'firstName': 'Tim',
               'lastName': 'S',
               'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
                'suffix': '/-Z4YK4VKE0JSVXIY1.jpg'}},
              {'id': '87473404',
               'firstName': 'TenantKing.com',
               'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
                'suffix': '/87473404-HI5DTBTK0HX401CA.png'},
               'type': 'page'}]}],
           'summary': '4 likes'},
          'logView': True,
          'agreeCount': 4,
          'disagreeCount': 0,
          'todo': {'count': 0},
          'user': {'id': '87473404',
           'firstName': 'TenantKing.com',
           'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
            'suffix': '/87473404-HI5DTBTK0HX401CA.png'},
           'type': 'page'}}]}]},
     'shortUrl': 'http://4sq.com/JNblHV',
     'timeZone': 'America/New_York',
     'listed': {'count': 54,
      'groups': [{'type': 'others',
        'name': 'Lists from other people',
        'count': 54,
        'items': [{'id': '4fa32fd0e4b04193744746b1',
          'name': 'Manhattan Haunts',
          'description': '',
          'type': 'others',
          'user': {'id': '24592223',
           'firstName': 'Becca',
           'lastName': 'M',
           'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
            'suffix': '/24592223-RAW2UYM0GIB1U40K.jpg'}},
          'editable': False,
          'public': True,
          'collaborative': False,
          'url': '/becca_mcarthur/list/manhattan-haunts',
          'canonicalUrl': 'https://foursquare.com/becca_mcarthur/list/manhattan-haunts',
          'createdAt': 1336094672,
          'updatedAt': 1380845377,
          'photo': {'id': '4e8cc9461081e3b3544e12e5',
           'createdAt': 1317849414,
           'prefix': 'https://fastly.4sqi.net/img/general/',
           'suffix': '/0NLVU2HC1JF4DXIMKWUFW3QBUT31DC11EFNYYHMJG3NDWAPS.jpg',
           'width': 492,
           'height': 330,
           'user': {'id': '742542',
            'firstName': 'Time Out New York',
            'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
             'suffix': '/XXHKCBSQHBORZNSR.jpg'},
            'type': 'page'},
           'visibility': 'public'},
          'followers': {'count': 22},
          'listItems': {'count': 187,
           'items': [{'id': 'v4fa862b3e4b0ebff2f749f06',
             'createdAt': 1342934485}]}},
         {'id': '4fae817be4b085f6b2a74d19',
          'name': 'USA NYC MAN FiDi',
          'description': 'Where to go for decent eats in the restaurant wasteland of Downtown NYC aka FiDi, along with Tribeca & Battery Park City.',
          'type': 'others',
          'user': {'id': '12113441',
           'firstName': 'Kino',
           'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
            'suffix': '/12113441-K5HTHFLU2MUCM0CM.jpg'}},
          'editable': False,
          'public': True,
          'collaborative': False,
          'url': '/kinosfault/list/usa-nyc-man-fidi',
          'canonicalUrl': 'https://foursquare.com/kinosfault/list/usa-nyc-man-fidi',
          'createdAt': 1336836475,
          'updatedAt': 1556754919,
          'photo': {'id': '55984992498e13ba75e353bb',
           'createdAt': 1436043666,
           'prefix': 'https://fastly.4sqi.net/img/general/',
           'suffix': '/12113441_iOa6Uh-Xi8bhj2-gpzkkw8MKiAIs7RmOcz_RM7m8ink.jpg',
           'width': 540,
           'height': 960,
           'user': {'id': '12113441',
            'firstName': 'Kino',
            'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
             'suffix': '/12113441-K5HTHFLU2MUCM0CM.jpg'}},
           'visibility': 'public'},
          'followers': {'count': 20},
          'listItems': {'count': 273,
           'items': [{'id': 'v4fa862b3e4b0ebff2f749f06',
             'createdAt': 1373909433}]}},
         {'id': '4fddeff0e4b0e078037ac0d3',
          'name': 'NYC Resturants',
          'description': '',
          'type': 'others',
          'user': {'id': '21563126',
           'firstName': 'Richard',
           'lastName': 'R',
           'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
            'suffix': '/21563126_v05J1KPw_SVj6Ehq9g8B9jeAGjFUMsU5QGl-NZ8inUQ7pKQm5bKplW37EmR7jS2A7GYPBBAtl.jpg'}},
          'editable': False,
          'public': True,
          'collaborative': True,
          'url': '/rickr7/list/nyc-resturants',
          'canonicalUrl': 'https://foursquare.com/rickr7/list/nyc-resturants',
          'createdAt': 1339944944,
          'updatedAt': 1591664261,
          'photo': {'id': '5072dd13e4b09145cdf782d1',
           'createdAt': 1349704979,
           'prefix': 'https://fastly.4sqi.net/img/general/',
           'suffix': '/208205_fGh2OuAZ9qJ4agbAA5wMVNOSIm9kNUlRtNwj1N-adqg.jpg',
           'width': 800,
           'height': 800,
           'user': {'id': '208205',
            'firstName': 'Thalia',
            'lastName': 'K',
            'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
             'suffix': '/SNOOLCAW2AG04ZKD.jpg'}},
           'visibility': 'public'},
          'followers': {'count': 12},
          'listItems': {'count': 193,
           'items': [{'id': 'v4fa862b3e4b0ebff2f749f06',
             'createdAt': 1581655865}]}},
         {'id': '5266c68a498e7c667807fe09',
          'name': 'Foodie Love in NY - 02',
          'description': '',
          'type': 'others',
          'user': {'id': '547977',
           'firstName': 'WiLL',
           'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
            'suffix': '/-Q5NYGDMFDMOITQRR.jpg'}},
          'editable': False,
          'public': True,
          'collaborative': False,
          'url': '/sweetiewill/list/foodie-love-in-ny--02',
          'canonicalUrl': 'https://foursquare.com/sweetiewill/list/foodie-love-in-ny--02',
          'createdAt': 1382467210,
          'updatedAt': 1391995585,
          'followers': {'count': 7},
          'listItems': {'count': 200,
           'items': [{'id': 'v4fa862b3e4b0ebff2f749f06',
             'createdAt': 1386809936}]}}]}]},
     'hours': {'status': 'Closed until 11:30 AM',
      'richStatus': {'entities': [], 'text': 'Closed until 11:30 AM'},
      'isOpen': False,
      'isLocalHoliday': False,
      'dayData': [],
      'timeframes': [{'days': 'Mon–Wed, Sun',
        'includesToday': True,
        'open': [{'renderedTime': '11:30 AM–11:00 PM'}],
        'segments': []},
       {'days': 'Thu–Sat',
        'open': [{'renderedTime': '11:30 AM–Midnight'}],
        'segments': []}]},
     'popular': {'isOpen': False,
      'isLocalHoliday': False,
      'timeframes': [{'days': 'Today',
        'includesToday': True,
        'open': [{'renderedTime': 'Noon–2:00 PM'},
         {'renderedTime': '6:00 PM–8:00 PM'}],
        'segments': []},
       {'days': 'Tue–Thu',
        'open': [{'renderedTime': 'Noon–2:00 PM'},
         {'renderedTime': '5:00 PM–10:00 PM'}],
        'segments': []},
       {'days': 'Fri',
        'open': [{'renderedTime': 'Noon–3:00 PM'},
         {'renderedTime': '5:00 PM–11:00 PM'}],
        'segments': []},
       {'days': 'Sat',
        'open': [{'renderedTime': 'Noon–11:00 PM'}],
        'segments': []},
       {'days': 'Sun',
        'open': [{'renderedTime': 'Noon–3:00 PM'},
         {'renderedTime': '5:00 PM–8:00 PM'}],
        'segments': []}]},
     'seasonalHours': [],
     'defaultHours': {'status': 'Closed until 11:30 AM',
      'richStatus': {'entities': [], 'text': 'Closed until 11:30 AM'},
      'isOpen': False,
      'isLocalHoliday': False,
      'dayData': [],
      'timeframes': [{'days': 'Mon–Wed, Sun',
        'includesToday': True,
        'open': [{'renderedTime': '11:30 AM–11:00 PM'}],
        'segments': []},
       {'days': 'Thu–Sat',
        'open': [{'renderedTime': '11:30 AM–Midnight'}],
        'segments': []}]},
     'pageUpdates': {'count': 0, 'items': []},
     'inbox': {'count': 0, 'items': []},
     'attributes': {'groups': [{'type': 'price',
        'name': 'Price',
        'summary': '$$',
        'count': 1,
        'items': [{'displayName': 'Price', 'displayValue': '$$', 'priceTier': 2}]},
       {'type': 'payments',
        'name': 'Credit Cards',
        'summary': 'Credit Cards',
        'count': 7,
        'items': [{'displayName': 'Credit Cards',
          'displayValue': 'Yes (incl. American Express)'}]},
       {'type': 'outdoorSeating',
        'name': 'Outdoor Seating',
        'summary': 'Outdoor Seating',
        'count': 1,
        'items': [{'displayName': 'Outdoor Seating', 'displayValue': 'Yes'}]},
       {'type': 'serves',
        'name': 'Menus',
        'summary': 'Happy Hour, Brunch & more',
        'count': 8,
        'items': [{'displayName': 'Brunch', 'displayValue': 'Brunch'},
         {'displayName': 'Lunch', 'displayValue': 'Lunch'},
         {'displayName': 'Dinner', 'displayValue': 'Dinner'},
         {'displayName': 'Happy Hour', 'displayValue': 'Happy Hour'}]},
       {'type': 'drinks',
        'name': 'Drinks',
        'summary': 'Beer, Wine & Cocktails',
        'count': 5,
        'items': [{'displayName': 'Beer', 'displayValue': 'Beer'},
         {'displayName': 'Wine', 'displayValue': 'Wine'},
         {'displayName': 'Cocktails', 'displayValue': 'Cocktails'}]}]},
     'bestPhoto': {'id': '4fad980de4b091b4626c3633',
      'createdAt': 1336776717,
      'source': {'name': 'Foursquare for Android',
       'url': 'https://foursquare.com/download/#/android'},
      'prefix': 'https://fastly.4sqi.net/img/general/',
      'suffix': '/ya1iQFI7pLjuIJp1PGDKlrZS3OJdHCF7tpILMmjv_2w.jpg',
      'width': 480,
      'height': 640,
      'visibility': 'public'},
     'colors': {'highlightColor': {'photoId': '4fad980de4b091b4626c3633',
       'value': -13619152},
      'highlightTextColor': {'photoId': '4fad980de4b091b4626c3633', 'value': -1},
      'algoVersion': 3}}



### B. Get the venue's overall rating


```python
try:
    print(result['response']['venue']['rating'])
except:
    print('This venue has not been rated yet.')
```

    6.9


That is not a very good rating. Let's check the rating of the second closest Italian restaurant.


```python
venue_id = '4f3232e219836c91c7bfde94' # ID of Conca Cucina Italian Restaurant
url = 'https://api.foursquare.com/v2/venues/{}?client_id={}&client_secret={}&v={}'.format(venue_id, CLIENT_ID, CLIENT_SECRET, VERSION)

result = requests.get(url).json()
try:
    print(result['response']['venue']['rating'])
except:
    print('This venue has not been rated yet.')
```

    This venue has not been rated yet.


Since this restaurant has no ratings, let's check the third restaurant.


```python
venue_id = '3fd66200f964a520f4e41ee3' # ID of Ecco
url = 'https://api.foursquare.com/v2/venues/{}?client_id={}&client_secret={}&v={}'.format(venue_id, CLIENT_ID, CLIENT_SECRET, VERSION)

result = requests.get(url).json()
try:
    print(result['response']['venue']['rating'])
except:
    print('This venue has not been rated yet.')
```

    7.3


Since this restaurant has a slightly better rating, let's explore it further.

### C. Get the number of tips


```python
result['response']['venue']['tips']['count']
```




    19



### D. Get the venue's tips
> `https://api.foursquare.com/v2/venues/`**VENUE_ID**`/tips?client_id=`**CLIENT_ID**`&client_secret=`**CLIENT_SECRET**`&v=`**VERSION**`&limit=`**LIMIT**

#### Create URL and send GET request. Make sure to set limit to get all tips


```python
## Ecco Tips
limit = 15 # set limit to be greater than or equal to the total number of tips
url = 'https://api.foursquare.com/v2/venues/{}/tips?client_id={}&client_secret={}&v={}&limit={}'.format(venue_id, CLIENT_ID, CLIENT_SECRET, VERSION, limit)

results = requests.get(url).json()
results
```




    {'meta': {'code': 200, 'requestId': '5f1ec660241e64486cdd76db'},
     'response': {'tips': {'count': 19,
       'items': [{'id': '5ab1cb46c9a517174651d3fe',
         'createdAt': 1521601350,
         'text': 'A+ Italian food! Trust me on this: my mom’s side of the family is 100% Italian. I was born and bred to know good pasta when I see it, and Ecco is one of my all-time NYC favorites',
         'type': 'user',
         'canonicalUrl': 'https://foursquare.com/item/5ab1cb46c9a517174651d3fe',
         'lang': 'en',
         'likes': {'count': 0, 'groups': []},
         'logView': True,
         'agreeCount': 4,
         'disagreeCount': 0,
         'todo': {'count': 0},
         'user': {'id': '484542633',
          'firstName': 'Nick',
          'lastName': 'E',
          'photo': {'prefix': 'https://fastly.4sqi.net/img/user/',
           'suffix': '/484542633_unymNUmw_FdPs3GjXHujmHcYnN4hf8kEPADlOZuIrdcdm97VX3tFqL7fFNMNA_8Gl9NlU1GYg.jpg'}},
         'authorInteractionType': 'liked'}]}}}



#### Get tips and list of associated features


```python
tips = results['response']['tips']['items']

tip = results['response']['tips']['items'][0]
tip.keys()
```




    dict_keys(['id', 'createdAt', 'text', 'type', 'canonicalUrl', 'lang', 'likes', 'logView', 'agreeCount', 'disagreeCount', 'todo', 'user', 'authorInteractionType'])



#### Format column width and display all tips


```python
#pd.set_option('display.max_colwidth', -1)
pd.set_option('display.max_colwidth', None)

# tips_df = json_normalize(tips) # json normalize tips
tips_df = pd.json_normalize(tips) # json normalize tips

# columns to keep
filtered_columns = ['text', 'agreeCount', 'disagreeCount', 'id', 'user.firstName', 'user.lastName', 'user.gender', 'user.id']
#print(filtered_columns)
#tips_filtered = tips_df.loc[:, filtered_columns]
tips_filtered = tips_df.reindex(columns=filtered_columns)

# display tips
tips_filtered
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
      <th>text</th>
      <th>agreeCount</th>
      <th>disagreeCount</th>
      <th>id</th>
      <th>user.firstName</th>
      <th>user.lastName</th>
      <th>user.gender</th>
      <th>user.id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A+ Italian food! Trust me on this: my mom’s side of the family is 100% Italian. I was born and bred to know good pasta when I see it, and Ecco is one of my all-time NYC favorites</td>
      <td>4</td>
      <td>0</td>
      <td>5ab1cb46c9a517174651d3fe</td>
      <td>Nick</td>
      <td>E</td>
      <td>NaN</td>
      <td>484542633</td>
    </tr>
  </tbody>
</table>
</div>



Now remember that because we are using a personal developer account, then we can access only 2 of the restaurant's tips, instead of all 15 tips.

   

<a id="item3"></a>

## 3. Search a Foursquare User
> `https://api.foursquare.com/v2/users/`**USER_ID**`?client_id=`**CLIENT_ID**`&client_secret=`**CLIENT_SECRET**`&v=`**VERSION**

### Define URL, send GET request and display features associated with user


```python
user_id = '484542633' # user ID with most agree counts and complete profile

url = 'https://api.foursquare.com/v2/users/{}?client_id={}&client_secret={}&v={}'.format(user_id, CLIENT_ID, CLIENT_SECRET, VERSION) # define URL
print(url)
# send GET request
results = requests.get(url).json()
print(results)
user_data = results['response']['user']

# display features associated with user
user_data.keys()
```

    https://api.foursquare.com/v2/users/484542633?client_id=1D4LUFF5Y0G2E2M52RW2QRFJLDT5CJH1QJUN0ANQLIJJCPU3&client_secret=05X3YLFANXLSHZ0J3RLUWTY3QGZ0D2ZCJQGSQDU5QBCCUEKL&v=20180604
    {'meta': {'code': 401, 'errorType': 'invalid_auth', 'errorDetail': 'Missing oauth_token. See https://developer.foursquare.com/docs/api/configuration/authentication for details.', 'requestId': '5f1ecb4fcbd37754cca73bbd'}, 'response': {}}



    ---------------------------------------------------------------------------

    KeyError                                  Traceback (most recent call last)

    <ipython-input-54-122bc403f5c5> in <module>
          6 results = requests.get(url).json()
          7 print(results)
    ----> 8 user_data = results['response']['user']
          9 
         10 # display features associated with user


    KeyError: 'user'



```python
print('First Name: ' + user_data['firstName'])
print('Last Name: ' + user_data['lastName'])
print('Home City: ' + user_data['homeCity'])
```

#### How many tips has this user submitted?


```python
user_data['tips']
```

Wow! So it turns out that Nick is a very active Foursquare user, with more than 250 tips.

### Get User's tips


```python
# define tips URL
url = 'https://api.foursquare.com/v2/users/{}/tips?client_id={}&client_secret={}&v={}&limit={}'.format(user_id, CLIENT_ID, CLIENT_SECRET, VERSION, limit)

# send GET request and get user's tips
results = requests.get(url).json()
tips = results['response']['tips']['items']

# format column width
#pd.set_option('display.max_colwidth', -1)
pd.set_option('display.max_colwidth', None)

# tips_df = json_normalize(tips)
tips_df = pd.json_normalize(tips)

# filter columns
filtered_columns = ['text', 'agreeCount', 'disagreeCount', 'id']
#tips_filtered = tips_df.loc[:, filtered_columns]
tips_filtered = tips_df.reindex(columns=filtered_columns)

# display user's tips
tips_filtered
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
      <th>text</th>
      <th>agreeCount</th>
      <th>disagreeCount</th>
      <th>id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>They serve coffee!!!!!!</td>
      <td>1</td>
      <td>0</td>
      <td>5accc98c0313204c9d7ec157</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Quick, cheap lunch that tastes good! Way shorter line than Chipotle, too.</td>
      <td>2</td>
      <td>0</td>
      <td>5acbec70a0215b732e264fe8</td>
    </tr>
    <tr>
      <th>2</th>
      <td>You’re not a real New Yorker until you’ve shame-ordered Insomnia Cookies for delivery at 3am</td>
      <td>1</td>
      <td>0</td>
      <td>5acbbd4eb1538e45373b07f5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Good for you yet still tasty! Clean green protein is my go-to after I hit the gym 💪</td>
      <td>2</td>
      <td>0</td>
      <td>5acbbcda01235808d5d6dc75</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Burger game strong 💪</td>
      <td>1</td>
      <td>0</td>
      <td>5ab575fb6bdee65f759da8c1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Great burgers &amp; fries! Also, this place is exactly what it’s like when you go to a bar in the Southwest. Source: I’m from Arizona.</td>
      <td>2</td>
      <td>0</td>
      <td>5ab5575d73fe2516ad8f363b</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Açaí bowl + peanut butter + whey protein = 💪💪💪</td>
      <td>1</td>
      <td>0</td>
      <td>5ab42db53c858d64af2688a4</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Highly underrated and way less crowded than Central Park!</td>
      <td>3</td>
      <td>0</td>
      <td>5ab42c396f706a29f53ad1a8</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Way easier to navigate than the Met proper, plus the Met Breuer focuses on modern art. If I only have a limited amount of time to spend in a museum, I would rather go here than anywhere else!</td>
      <td>6</td>
      <td>0</td>
      <td>5ab42b987dc9e17930e5ff5b</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Get the açaí bowl with peanut butter after your work out and thank me later 👌</td>
      <td>1</td>
      <td>0</td>
      <td>5ab42aca2a7ab6333652b266</td>
    </tr>
    <tr>
      <th>10</th>
      <td>When you want a burger, this should be the first thing that comes to mind. A+!</td>
      <td>1</td>
      <td>0</td>
      <td>5ab42a28da5e5617d18e3a6a</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Way less crowded than Central Park! People who live in the neighborhood rave about Carl Schurz Park.</td>
      <td>3</td>
      <td>0</td>
      <td>5ab429db1ffe971b060083f5</td>
    </tr>
    <tr>
      <th>12</th>
      <td>The best Mexican food in the Murray Hill / Kips Bay area!</td>
      <td>1</td>
      <td>0</td>
      <td>5ab3f53f8496ca57542d5549</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Best coffee shop in the neighborhood!</td>
      <td>1</td>
      <td>0</td>
      <td>5ab3f428da5e5617d17d1475</td>
    </tr>
    <tr>
      <th>14</th>
      <td>When there’s nice weather, the rooftop at Tonic East is the best place to watch the game. Perfect for March Madness &amp; NBA finals!</td>
      <td>2</td>
      <td>0</td>
      <td>5ab3f3fedd70c572de886c9d</td>
    </tr>
  </tbody>
</table>
</div>



#### Let's get the venue for the tip with the greatest number of agree counts


```python
tip_id = '5ab5575d73fe2516ad8f363b' # tip id

# define URL
url = 'http://api.foursquare.com/v2/tips/{}?client_id={}&client_secret={}&v={}'.format(tip_id, CLIENT_ID, CLIENT_SECRET, VERSION)

# send GET Request and examine results
result = requests.get(url).json()
print(result['response']['tip']['venue']['name'])
print(result['response']['tip']['venue']['location'])
```

    Cowgirl
    {'address': '519 Hudson St', 'crossStreet': 'at W 10th St', 'lat': 40.73373338282062, 'lng': -74.0062998849649, 'labeledLatLngs': [{'label': 'display', 'lat': 40.73373338282062, 'lng': -74.0062998849649}], 'postalCode': '10014', 'cc': 'US', 'city': 'New York', 'state': 'NY', 'country': 'United States', 'formattedAddress': ['519 Hudson St (at W 10th St)', 'New York, NY 10014', 'United States']}


### Get User's friends


```python
# user_friends = json_normalize(user_data['friends']['groups'][0]['items'])
user_friends = pd.json_normalize(user_data['friends']['groups'][0]['items'])
user_friends
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-39-bcc27b1bf8b4> in <module>
    ----> 1 user_friends = json_normalize(user_data['friends']['groups'][0]['items'])
          2 user_friends


    NameError: name 'user_data' is not defined


Interesting. Despite being very active, it turns out that Nick does not have any friends on Foursquare. This might definitely change in the future.

### Retrieve the User's Profile Image


```python
user_data
```


```python
# 1. grab prefix of photo
# 2. grab suffix of photo
# 3. concatenate them using the image size  
Image(url='https://igx.4sqi.net/img/user/300x300/484542633_mK2Yum7T_7Tn9fWpndidJsmw2Hof_6T5vJBKCHPLMK5OL-U5ZiJGj51iwBstcpDLYa3Zvhvis.jpg')
```

  

<a id="item4"></a>

## 4. Explore a location
> `https://api.foursquare.com/v2/venues/`**explore**`?client_id=`**CLIENT_ID**`&client_secret=`**CLIENT_SECRET**`&ll=`**LATITUDE**`,`**LONGITUDE**`&v=`**VERSION**`&limit=`**LIMIT**

#### So, you just finished your gourmet dish at Ecco, and are just curious about the popular spots around the restaurant. In order to explore the area, let's start by getting the latitude and longitude values of Ecco Restaurant.


```python
latitude = 40.715337
longitude = -74.008848
```

#### Define URL


```python
url = 'https://api.foursquare.com/v2/venues/explore?client_id={}&client_secret={}&ll={},{}&v={}&radius={}&limit={}'.format(CLIENT_ID, CLIENT_SECRET, latitude, longitude, VERSION, radius, LIMIT)
url
```




    'https://api.foursquare.com/v2/venues/explore?client_id=1D4LUFF5Y0G2E2M52RW2QRFJLDT5CJH1QJUN0ANQLIJJCPU3&client_secret=05X3YLFANXLSHZ0J3RLUWTY3QGZ0D2ZCJQGSQDU5QBCCUEKL&ll=40.715337,-74.008848&v=20180604&radius=500&limit=30'



#### Send GET request and examine results


```python
import requests
```


```python
results = requests.get(url).json()
'There are {} around Ecco restaurant.'.format(len(results['response']['groups'][0]['items']))
```




    'There are 30 around Ecco restaurant.'



#### Get relevant part of JSON


```python
items = results['response']['groups'][0]['items']
items[0]
```




    {'reasons': {'count': 0,
      'items': [{'summary': 'This spot is popular',
        'type': 'general',
        'reasonName': 'globalInteractionReason'}]},
     'venue': {'id': '5d5f24ec09484500079aee00',
      'name': 'Los Tacos No. 1',
      'location': {'address': '136 Church St',
       'lat': 40.714267,
       'lng': -74.008756,
       'labeledLatLngs': [{'label': 'display',
         'lat': 40.714267,
         'lng': -74.008756}],
       'distance': 119,
       'postalCode': '10007',
       'cc': 'US',
       'city': 'New York',
       'state': 'NY',
       'country': 'United States',
       'formattedAddress': ['136 Church St',
        'New York, NY 10007',
        'United States']},
      'categories': [{'id': '4bf58dd8d48988d151941735',
        'name': 'Taco Place',
        'pluralName': 'Taco Places',
        'shortName': 'Tacos',
        'icon': {'prefix': 'https://ss3.4sqi.net/img/categories_v2/food/taco_',
         'suffix': '.png'},
        'primary': True}],
      'delivery': {'id': '2180700',
       'url': 'https://www.seamless.com/menu/los-tacos-no-1-tribeca-136-church-st-new-york/2180700?affiliate=1131&utm_source=foursquare-affiliate-network&utm_medium=affiliate&utm_campaign=1131&utm_content=2180700',
       'provider': {'name': 'seamless',
        'icon': {'prefix': 'https://fastly.4sqi.net/img/general/cap/',
         'sizes': [40, 50],
         'name': '/delivery_provider_seamless_20180129.png'}}},
      'photos': {'count': 0, 'groups': []}},
     'referralId': 'e-0-5d5f24ec09484500079aee00-0'}



#### Process JSON and convert it to a clean dataframe


```python
# dataframe = json_normalize(items) # flatten JSON
dataframe = pd.json_normalize(items) # flatten JSON

# filter columns
filtered_columns = ['venue.name', 'venue.categories'] + [col for col in dataframe.columns if col.startswith('venue.location.')] + ['venue.id']
# dataframe_filtered = dataframe.loc[:, filtered_columns]
dataframe_filtered = dataframe.reindex(columns=filtered_columns)

# filter the category for each row
dataframe_filtered['venue.categories'] = dataframe_filtered.apply(get_category_type, axis=1)

# clean columns
dataframe_filtered.columns = [col.split('.')[-1] for col in dataframe_filtered.columns]

dataframe_filtered.head(10)
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
      <th>name</th>
      <th>categories</th>
      <th>address</th>
      <th>lat</th>
      <th>lng</th>
      <th>labeledLatLngs</th>
      <th>distance</th>
      <th>postalCode</th>
      <th>cc</th>
      <th>city</th>
      <th>state</th>
      <th>country</th>
      <th>formattedAddress</th>
      <th>crossStreet</th>
      <th>neighborhood</th>
      <th>id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Los Tacos No. 1</td>
      <td>Taco Place</td>
      <td>136 Church St</td>
      <td>40.714267</td>
      <td>-74.008756</td>
      <td>[{'label': 'display', 'lat': 40.714267, 'lng': -74.008756}]</td>
      <td>119</td>
      <td>10007</td>
      <td>US</td>
      <td>New York</td>
      <td>NY</td>
      <td>United States</td>
      <td>[136 Church St, New York, NY 10007, United States]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5d5f24ec09484500079aee00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Korin</td>
      <td>Furniture / Home Store</td>
      <td>57 Warren St</td>
      <td>40.714824</td>
      <td>-74.009404</td>
      <td>[{'label': 'display', 'lat': 40.71482437714839, 'lng': -74.00940425461492}, {'label': 'entrance', 'lat': 40.714727, 'lng': -74.009399}]</td>
      <td>73</td>
      <td>10007</td>
      <td>US</td>
      <td>New York</td>
      <td>NY</td>
      <td>United States</td>
      <td>[57 Warren St (Church St), New York, NY 10007, United States]</td>
      <td>Church St</td>
      <td>Tribeca</td>
      <td>4af5d65ff964a52091fd21e3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Takahachi Bakery</td>
      <td>Bakery</td>
      <td>25 Murray St</td>
      <td>40.713653</td>
      <td>-74.008804</td>
      <td>[{'label': 'display', 'lat': 40.713652845301894, 'lng': -74.0088038953017}, {'label': 'entrance', 'lat': 40.713716, 'lng': -74.008443}]</td>
      <td>187</td>
      <td>10007</td>
      <td>US</td>
      <td>New York</td>
      <td>NY</td>
      <td>United States</td>
      <td>[25 Murray St (at Church St), New York, NY 10007, United States]</td>
      <td>at Church St</td>
      <td>NaN</td>
      <td>4c154c9a77cea593c401d260</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Juice Press</td>
      <td>Vegetarian / Vegan Restaurant</td>
      <td>83 Murray St</td>
      <td>40.714788</td>
      <td>-74.011132</td>
      <td>[{'label': 'display', 'lat': 40.71478769908051, 'lng': -74.0111317502157}]</td>
      <td>202</td>
      <td>10007</td>
      <td>US</td>
      <td>New York</td>
      <td>NY</td>
      <td>United States</td>
      <td>[83 Murray St (btwn Greenwich St &amp; W Broadway), New York, NY 10007, United States]</td>
      <td>btwn Greenwich St &amp; W Broadway</td>
      <td>NaN</td>
      <td>54148bc6498ea7bb8c05b70a</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Takahachi</td>
      <td>Sushi Restaurant</td>
      <td>145 Duane St</td>
      <td>40.716526</td>
      <td>-74.008101</td>
      <td>[{'label': 'display', 'lat': 40.71652647412374, 'lng': -74.00810108466207}, {'label': 'entrance', 'lat': 40.716508, 'lng': -74.007989}]</td>
      <td>146</td>
      <td>10013</td>
      <td>US</td>
      <td>New York</td>
      <td>NY</td>
      <td>United States</td>
      <td>[145 Duane St (btwn W Broadway &amp; Church St), New York, NY 10013, United States]</td>
      <td>btwn W Broadway &amp; Church St</td>
      <td>NaN</td>
      <td>4a8f2f39f964a520471420e3</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Chambers Street Wines</td>
      <td>Wine Shop</td>
      <td>148 Chambers St</td>
      <td>40.715773</td>
      <td>-74.009718</td>
      <td>[{'label': 'display', 'lat': 40.715773063928374, 'lng': -74.00971823312332}, {'label': 'entrance', 'lat': 40.715696, 'lng': -74.00988}]</td>
      <td>88</td>
      <td>10007</td>
      <td>US</td>
      <td>New York</td>
      <td>NY</td>
      <td>United States</td>
      <td>[148 Chambers St (btwn West Broadway &amp; Hudson St), New York, NY 10007, United States]</td>
      <td>btwn West Broadway &amp; Hudson St</td>
      <td>NaN</td>
      <td>4adcf23cf964a520cc6221e3</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Nish Nūsh</td>
      <td>Falafel Restaurant</td>
      <td>88 Reade St</td>
      <td>40.715537</td>
      <td>-74.007725</td>
      <td>[{'label': 'display', 'lat': 40.71553710116416, 'lng': -74.00772452925565}, {'label': 'entrance', 'lat': 40.715615, 'lng': -74.00773}]</td>
      <td>97</td>
      <td>10013</td>
      <td>US</td>
      <td>New York</td>
      <td>NY</td>
      <td>United States</td>
      <td>[88 Reade St (at Church St), New York, NY 10013, United States]</td>
      <td>at Church St</td>
      <td>NaN</td>
      <td>50ba9119e4b071a4bae6dc10</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Restaurant Marc Forgione</td>
      <td>New American Restaurant</td>
      <td>134 Reade St</td>
      <td>40.716380</td>
      <td>-74.009629</td>
      <td>[{'label': 'display', 'lat': 40.71637984317071, 'lng': -74.00962933453428}]</td>
      <td>133</td>
      <td>10013</td>
      <td>US</td>
      <td>New York</td>
      <td>NY</td>
      <td>United States</td>
      <td>[134 Reade St (btwn Hudson and Greenwich St), New York, NY 10013, United States]</td>
      <td>btwn Hudson and Greenwich St</td>
      <td>NaN</td>
      <td>48510cf9f964a520a5501fe3</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Weather Up</td>
      <td>Cocktail Bar</td>
      <td>159 Duane St</td>
      <td>40.716741</td>
      <td>-74.008666</td>
      <td>[{'label': 'display', 'lat': 40.71674084163369, 'lng': -74.0086664438893}, {'label': 'entrance', 'lat': 40.71685, 'lng': -74.008729}]</td>
      <td>157</td>
      <td>10013</td>
      <td>US</td>
      <td>New York</td>
      <td>NY</td>
      <td>United States</td>
      <td>[159 Duane St (btwn Hudson St. &amp; W Broadway), New York, NY 10013, United States]</td>
      <td>btwn Hudson St. &amp; W Broadway</td>
      <td>NaN</td>
      <td>4cd89eeb6e8b5941660c64d2</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Lekka Burger</td>
      <td>Burger Joint</td>
      <td>81 Warren St</td>
      <td>40.715246</td>
      <td>-74.010559</td>
      <td>[{'label': 'display', 'lat': 40.715246, 'lng': -74.010559}]</td>
      <td>144</td>
      <td>10007</td>
      <td>US</td>
      <td>New York</td>
      <td>NY</td>
      <td>United States</td>
      <td>[81 Warren St (btw Greenwich &amp; West Broadway), New York, NY 10007, United States]</td>
      <td>btw Greenwich &amp; West Broadway</td>
      <td>NaN</td>
      <td>5dc6f6a5ea8dfb00080f6faa</td>
    </tr>
  </tbody>
</table>
</div>



#### Let's visualize these items on the map around our location


```python
venues_map = folium.Map(location=[latitude, longitude], zoom_start=15) # generate map centred around Ecco


# add Ecco as a red circle mark
folium.features.CircleMarker(
    [latitude, longitude],
    radius=10,
    popup='Ecco',
    fill=True,
    color='red',
    fill_color='red',
    fill_opacity=0.6
    ).add_to(venues_map)


# add popular spots to the map as blue circle markers
for lat, lng, label in zip(dataframe_filtered.lat, dataframe_filtered.lng, dataframe_filtered.categories):
    folium.features.CircleMarker(
        [lat, lng],
        radius=5,
        popup=label,
        fill=True,
        color='blue',
        fill_color='blue',
        fill_opacity=0.6
        ).add_to(venues_map)

# display map
venues_map
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe src="about:blank" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" data-html=PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfZjZjYjhhZDU1MWRiNGFiNWI5Zjg3NzZkZGY0ZjcwYjggewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwX2Y2Y2I4YWQ1NTFkYjRhYjViOWY4Nzc2ZGRmNGY3MGI4IiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCcsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNDAuNzE1MzM3LC03NC4wMDg4NDhdLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgem9vbTogMTUsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBtYXhCb3VuZHM6IGJvdW5kcywKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGxheWVyczogW10sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB3b3JsZENvcHlKdW1wOiBmYWxzZSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGNyczogTC5DUlMuRVBTRzM4NTcKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciB0aWxlX2xheWVyXzgzZmU5YjVlNTNhMzQ4Mzc5ZjE3NDNhNmQ0NGVmYWEyID0gTC50aWxlTGF5ZXIoCiAgICAgICAgICAgICAgICAnaHR0cHM6Ly97c30udGlsZS5vcGVuc3RyZWV0bWFwLm9yZy97en0ve3h9L3t5fS5wbmcnLAogICAgICAgICAgICAgICAgewogICJhdHRyaWJ1dGlvbiI6IG51bGwsCiAgImRldGVjdFJldGluYSI6IGZhbHNlLAogICJtYXhab29tIjogMTgsCiAgIm1pblpvb20iOiAxLAogICJub1dyYXAiOiBmYWxzZSwKICAic3ViZG9tYWlucyI6ICJhYmMiCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2Y2Y2I4YWQ1NTFkYjRhYjViOWY4Nzc2ZGRmNGY3MGI4KTsKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mNmUzNjE5NzkxMmQ0OTUzODgwNmE2OTRiMzA2ZTFlMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjcxNTMzNywtNzQuMDA4ODQ4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInJlZCIsCiAgImZpbGxPcGFjaXR5IjogMC42LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogMTAsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZjZjYjhhZDU1MWRiNGFiNWI5Zjg3NzZkZGY0ZjcwYjgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDVmZjJiZmIzNzNmNGE1OGI2NGNkY2YxYWVlMzIzNjggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYWI4ZDQ2MGU0MTY2NDU2OWI4MzU5NWI1N2IyYTM0ZDcgPSAkKCc8ZGl2IGlkPSJodG1sX2FiOGQ0NjBlNDE2NjQ1NjliODM1OTViNTdiMmEzNGQ3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5FY2NvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kNWZmMmJmYjM3M2Y0YTU4YjY0Y2RjZjFhZWUzMjM2OC5zZXRDb250ZW50KGh0bWxfYWI4ZDQ2MGU0MTY2NDU2OWI4MzU5NWI1N2IyYTM0ZDcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjZlMzYxOTc5MTJkNDk1Mzg4MDZhNjk0YjMwNmUxZTIuYmluZFBvcHVwKHBvcHVwX2Q1ZmYyYmZiMzczZjRhNThiNjRjZGNmMWFlZTMyMzY4KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2RiYjk1NDRhMzE2ZTQ2NzJhNDhiN2U2ODEwNTQwODg3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuNzE0MjY3LC03NC4wMDg3NTZdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuNiwKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZjZjYjhhZDU1MWRiNGFiNWI5Zjg3NzZkZGY0ZjcwYjgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWFkZTgwMmIxMDI3NGE0Njg4MGU3MzU1OWVmZmZmNWIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNTdmMzg4NzNjMTdlNDVmZTgwZTdhYWQ5MjdiYjRlYjEgPSAkKCc8ZGl2IGlkPSJodG1sXzU3ZjM4ODczYzE3ZTQ1ZmU4MGU3YWFkOTI3YmI0ZWIxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UYWNvIFBsYWNlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hYWRlODAyYjEwMjc0YTQ2ODgwZTczNTU5ZWZmZmY1Yi5zZXRDb250ZW50KGh0bWxfNTdmMzg4NzNjMTdlNDVmZTgwZTdhYWQ5MjdiYjRlYjEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZGJiOTU0NGEzMTZlNDY3MmE0OGI3ZTY4MTA1NDA4ODcuYmluZFBvcHVwKHBvcHVwX2FhZGU4MDJiMTAyNzRhNDY4ODBlNzM1NTllZmZmZjViKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2ZiMjMzM2M2Njc5YjRkMDViOTAyNTZkYjA1NTQwN2ZlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuNzE0ODI0Mzc3MTQ4MzksLTc0LjAwOTQwNDI1NDYxNDkyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjYsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2Y2Y2I4YWQ1NTFkYjRhYjViOWY4Nzc2ZGRmNGY3MGI4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2FmZDY4NGNkN2I3ZDQ1ZmRiMmI2NTU1MGNkM2VjZWI3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2FjNGRhY2U1N2M1ZjQ5NjQ4MjAwOWNjMjUzYTAxYjZjID0gJCgnPGRpdiBpZD0iaHRtbF9hYzRkYWNlNTdjNWY0OTY0ODIwMDljYzI1M2EwMWI2YyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RnVybml0dXJlIC8gSG9tZSBTdG9yZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYWZkNjg0Y2Q3YjdkNDVmZGIyYjY1NTUwY2QzZWNlYjcuc2V0Q29udGVudChodG1sX2FjNGRhY2U1N2M1ZjQ5NjQ4MjAwOWNjMjUzYTAxYjZjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2ZiMjMzM2M2Njc5YjRkMDViOTAyNTZkYjA1NTQwN2ZlLmJpbmRQb3B1cChwb3B1cF9hZmQ2ODRjZDdiN2Q0NWZkYjJiNjU1NTBjZDNlY2ViNyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wNzZiMTU4ZGE0YmM0NGM1OTY2MWNhMDQ4NGZkMDBjMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjcxMzY1Mjg0NTMwMTg5NCwtNzQuMDA4ODAzODk1MzAxN10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC42LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83OTAwNWRiZjBjMjA0MjcyYTRkOWIwMGQzNmFkNDc4YiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jYTdjMTdlZmIwOGQ0Yzk2OTgyODQyZDcyNGY5ODcwMSA9ICQoJzxkaXYgaWQ9Imh0bWxfY2E3YzE3ZWZiMDhkNGM5Njk4Mjg0MmQ3MjRmOTg3MDEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJha2VyeTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzkwMDVkYmYwYzIwNDI3MmE0ZDliMDBkMzZhZDQ3OGIuc2V0Q29udGVudChodG1sX2NhN2MxN2VmYjA4ZDRjOTY5ODI4NDJkNzI0Zjk4NzAxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzA3NmIxNThkYTRiYzQ0YzU5NjYxY2EwNDg0ZmQwMGMxLmJpbmRQb3B1cChwb3B1cF83OTAwNWRiZjBjMjA0MjcyYTRkOWIwMGQzNmFkNDc4Yik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8xZDljNjA5NTFjYzQ0NjBhOGEzNWZiZWJlZDJiMGZhMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjcxNDc4NzY5OTA4MDUxLC03NC4wMTExMzE3NTAyMTU3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjYsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2Y2Y2I4YWQ1NTFkYjRhYjViOWY4Nzc2ZGRmNGY3MGI4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzhlNDY1NzYwMjlmMTQ4ZmVhNzUxZGU4MDFhM2Y2MGNlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2NmODAwYzQyMjllNzQ0NTA5ODNlZTY0MzRmZWNlOTUyID0gJCgnPGRpdiBpZD0iaHRtbF9jZjgwMGM0MjI5ZTc0NDUwOTgzZWU2NDM0ZmVjZTk1MiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VmVnZXRhcmlhbiAvIFZlZ2FuIFJlc3RhdXJhbnQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzhlNDY1NzYwMjlmMTQ4ZmVhNzUxZGU4MDFhM2Y2MGNlLnNldENvbnRlbnQoaHRtbF9jZjgwMGM0MjI5ZTc0NDUwOTgzZWU2NDM0ZmVjZTk1Mik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xZDljNjA5NTFjYzQ0NjBhOGEzNWZiZWJlZDJiMGZhMi5iaW5kUG9wdXAocG9wdXBfOGU0NjU3NjAyOWYxNDhmZWE3NTFkZTgwMWEzZjYwY2UpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjhiNGQwM2VjMDA1NDMyNThhOTY1ODgyNGViOGQ1ZDMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0MC43MTY1MjY0NzQxMjM3NCwtNzQuMDA4MTAxMDg0NjYyMDddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuNiwKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZjZjYjhhZDU1MWRiNGFiNWI5Zjg3NzZkZGY0ZjcwYjgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODM1MzIzMmI1MzdlNGQ3YzhlOTAzMTExOTAwNGEyNGMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYTMwNzE5ZTAyNWMyNDllNjhjOThiOGQ1ZGVmZDYwNjEgPSAkKCc8ZGl2IGlkPSJodG1sX2EzMDcxOWUwMjVjMjQ5ZTY4Yzk4YjhkNWRlZmQ2MDYxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdXNoaSBSZXN0YXVyYW50PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF84MzUzMjMyYjUzN2U0ZDdjOGU5MDMxMTE5MDA0YTI0Yy5zZXRDb250ZW50KGh0bWxfYTMwNzE5ZTAyNWMyNDllNjhjOThiOGQ1ZGVmZDYwNjEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMjhiNGQwM2VjMDA1NDMyNThhOTY1ODgyNGViOGQ1ZDMuYmluZFBvcHVwKHBvcHVwXzgzNTMyMzJiNTM3ZTRkN2M4ZTkwMzExMTkwMDRhMjRjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2NjMTQ0NzUyNzcwMDQ2YmY5NTBhYmIyNGY5ZjMzNTBlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuNzE1NzczMDYzOTI4Mzc0LC03NC4wMDk3MTgyMzMxMjMzMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC42LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82ODY3OGI1OGYxZjc0MjJmODdmNTkwN2U1ZGFhOTZkMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lYzQxM2VmMTE4ZmU0M2VmOGZjODZkODhkMDdlOWFhOCA9ICQoJzxkaXYgaWQ9Imh0bWxfZWM0MTNlZjExOGZlNDNlZjhmYzg2ZDg4ZDA3ZTlhYTgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPldpbmUgU2hvcDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjg2NzhiNThmMWY3NDIyZjg3ZjU5MDdlNWRhYTk2ZDIuc2V0Q29udGVudChodG1sX2VjNDEzZWYxMThmZTQzZWY4ZmM4NmQ4OGQwN2U5YWE4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2NjMTQ0NzUyNzcwMDQ2YmY5NTBhYmIyNGY5ZjMzNTBlLmJpbmRQb3B1cChwb3B1cF82ODY3OGI1OGYxZjc0MjJmODdmNTkwN2U1ZGFhOTZkMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lOGFhNmFhOTZiMGI0MmJlOTgxNDk2MGRlZjAzMzMxNiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjcxNTUzNzEwMTE2NDE2LC03NC4wMDc3MjQ1MjkyNTU2NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC42LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mMjZjOWQ5ZDMzZjU0YjEzOWU0MmZjYjM0MDM0Yzk1MCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83N2Q3NjM3ZGIzYzU0NTExYmRhZDNhNWVhOTRiODY1ZiA9ICQoJzxkaXYgaWQ9Imh0bWxfNzdkNzYzN2RiM2M1NDUxMWJkYWQzYTVlYTk0Yjg2NWYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZhbGFmZWwgUmVzdGF1cmFudDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZjI2YzlkOWQzM2Y1NGIxMzllNDJmY2IzNDAzNGM5NTAuc2V0Q29udGVudChodG1sXzc3ZDc2MzdkYjNjNTQ1MTFiZGFkM2E1ZWE5NGI4NjVmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2U4YWE2YWE5NmIwYjQyYmU5ODE0OTYwZGVmMDMzMzE2LmJpbmRQb3B1cChwb3B1cF9mMjZjOWQ5ZDMzZjU0YjEzOWU0MmZjYjM0MDM0Yzk1MCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83MGRhOTg2MDlmN2Y0NTg1YjZhYTE0OWMyY2Y3MDZiZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjcxNjM3OTg0MzE3MDcxLC03NC4wMDk2MjkzMzQ1MzQyOF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC42LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mMjFmMzVhNWM2NWI0ODNjODViNDU1MWNkNDVmNGI2ZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lNWUxNDQ5ZjU1ZGI0YWI5OWIwMTBjOTYwMDZiOGI2OCA9ICQoJzxkaXYgaWQ9Imh0bWxfZTVlMTQ0OWY1NWRiNGFiOTliMDEwYzk2MDA2YjhiNjgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5ldyBBbWVyaWNhbiBSZXN0YXVyYW50PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mMjFmMzVhNWM2NWI0ODNjODViNDU1MWNkNDVmNGI2Zi5zZXRDb250ZW50KGh0bWxfZTVlMTQ0OWY1NWRiNGFiOTliMDEwYzk2MDA2YjhiNjgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzBkYTk4NjA5ZjdmNDU4NWI2YWExNDljMmNmNzA2YmUuYmluZFBvcHVwKHBvcHVwX2YyMWYzNWE1YzY1YjQ4M2M4NWI0NTUxY2Q0NWY0YjZmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzM3MGZhN2U2YTkyZDQ3ZWFhY2ZlNjE5MzNhODVkOGNlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuNzE2NzQwODQxNjMzNjksLTc0LjAwODY2NjQ0Mzg4OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuNiwKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZjZjYjhhZDU1MWRiNGFiNWI5Zjg3NzZkZGY0ZjcwYjgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNTIwZjAyNzA3MjZkNDUzNDk5Y2YwNGYxZWZkOGYwN2QgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMmI0ZTY2NTNkYjdmNGVlNWEzOGRjYTE4YTYzYzY4ZTUgPSAkKCc8ZGl2IGlkPSJodG1sXzJiNGU2NjUzZGI3ZjRlZTVhMzhkY2ExOGE2M2M2OGU1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Db2NrdGFpbCBCYXI8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzUyMGYwMjcwNzI2ZDQ1MzQ5OWNmMDRmMWVmZDhmMDdkLnNldENvbnRlbnQoaHRtbF8yYjRlNjY1M2RiN2Y0ZWU1YTM4ZGNhMThhNjNjNjhlNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zNzBmYTdlNmE5MmQ0N2VhYWNmZTYxOTMzYTg1ZDhjZS5iaW5kUG9wdXAocG9wdXBfNTIwZjAyNzA3MjZkNDUzNDk5Y2YwNGYxZWZkOGYwN2QpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzM4Njc5MjY5ZTEzNDc5ZDgzMjljZTg0ZmI5NWRlOTAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0MC43MTUyNDYsLTc0LjAxMDU1OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC42LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xMGZhMTBmZWM4ZTQ0MGYyYTYzZGU3MTg2MDcxZDcyNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81MWQ0MTE2ODRmZGE0YzU1YjE3MjUzNDQ0ZTBmMTgwMSA9ICQoJzxkaXYgaWQ9Imh0bWxfNTFkNDExNjg0ZmRhNGM1NWIxNzI1MzQ0NGUwZjE4MDEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJ1cmdlciBKb2ludDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTBmYTEwZmVjOGU0NDBmMmE2M2RlNzE4NjA3MWQ3MjYuc2V0Q29udGVudChodG1sXzUxZDQxMTY4NGZkYTRjNTViMTcyNTM0NDRlMGYxODAxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzczODY3OTI2OWUxMzQ3OWQ4MzI5Y2U4NGZiOTVkZTkwLmJpbmRQb3B1cChwb3B1cF8xMGZhMTBmZWM4ZTQ0MGYyYTYzZGU3MTg2MDcxZDcyNik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jMDA3ZGQ0ODEyMmE0NWZlOTI4YzIyYjAzNGIyNzNiOSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjcxNTU3OTE1NTQyMDYwNiwtNzQuMDExMzY4MjM5NTgxMTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuNiwKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZjZjYjhhZDU1MWRiNGFiNWI5Zjg3NzZkZGY0ZjcwYjgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMWJjNmY1YzI1OTczNGNjMmJlNjIxODhmZGMzYmQzMDQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOGUwZDA5NWEzY2MxNDVkNzk0YzMzNTRhNDMwNzI2YjYgPSAkKCc8ZGl2IGlkPSJodG1sXzhlMGQwOTVhM2NjMTQ1ZDc5NGMzMzU0YTQzMDcyNmI2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Hcm9jZXJ5IFN0b3JlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xYmM2ZjVjMjU5NzM0Y2MyYmU2MjE4OGZkYzNiZDMwNC5zZXRDb250ZW50KGh0bWxfOGUwZDA5NWEzY2MxNDVkNzk0YzMzNTRhNDMwNzI2YjYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzAwN2RkNDgxMjJhNDVmZTkyOGMyMmIwMzRiMjczYjkuYmluZFBvcHVwKHBvcHVwXzFiYzZmNWMyNTk3MzRjYzJiZTYyMTg4ZmRjM2JkMzA0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2YyZTgxNThhZTNhODQ1MTk5MDhjODJiOGI0ZGYwMThkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuNzE2NzUyODE2ODc2NjM1LC03NC4wMDg1ODM3NjI5NTIyMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC42LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xZWRkZmFiNTg4YmY0NDhkOTk3YjJjNWQzNmQ2MDBmZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jNGIyYjM5ZDFiMjQ0M2Q4YTk2NGExYmYxNWEzOTU5MiA9ICQoJzxkaXYgaWQ9Imh0bWxfYzRiMmIzOWQxYjI0NDNkOGE5NjRhMWJmMTVhMzk1OTIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkFzaWFuIFJlc3RhdXJhbnQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzFlZGRmYWI1ODhiZjQ0OGQ5OTdiMmM1ZDM2ZDYwMGZkLnNldENvbnRlbnQoaHRtbF9jNGIyYjM5ZDFiMjQ0M2Q4YTk2NGExYmYxNWEzOTU5Mik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mMmU4MTU4YWUzYTg0NTE5OTA4YzgyYjhiNGRmMDE4ZC5iaW5kUG9wdXAocG9wdXBfMWVkZGZhYjU4OGJmNDQ4ZDk5N2IyYzVkMzZkNjAwZmQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjRhM2I3ZThhOGNhNDVjZWJlZDQ1YTBkZTQ0NmZkZTAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0MC43MTQwOTg2MDcyNjA0MSwtNzQuMDA5Njg1NzE3OTI4M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC42LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kMzliMDk3ZDliZDY0NTc4YTM2MWNmMmM4OGIyNzhmNCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kMWY0MTdjZWU5YTg0NTE5ODc0NGM0NTNhNzRiMDY5MCA9ICQoJzxkaXYgaWQ9Imh0bWxfZDFmNDE3Y2VlOWE4NDUxOTg3NDRjNDUzYTc0YjA2OTAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkd5bTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDM5YjA5N2Q5YmQ2NDU3OGEzNjFjZjJjODhiMjc4ZjQuc2V0Q29udGVudChodG1sX2QxZjQxN2NlZTlhODQ1MTk4NzQ0YzQ1M2E3NGIwNjkwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzI0YTNiN2U4YThjYTQ1Y2ViZWQ0NWEwZGU0NDZmZGUwLmJpbmRQb3B1cChwb3B1cF9kMzliMDk3ZDliZDY0NTc4YTM2MWNmMmM4OGIyNzhmNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wMWU1ZDg0YTRmODI0YjcxYmI1MDhiNWMwNTY5ZjQzMyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjcxNjc5MzA0ODU1ODA4LC03NC4wMDgyMTk5ODg3ODQ1N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC42LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wOTVjYmQ3YzFhMDI0MDJkYWI1M2RkZmExY2E0ZTY5YSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81MDRjODZiZTczMTQ0YWE0ODlhMzA5MDlkYzdlMzVmNSA9ICQoJzxkaXYgaWQ9Imh0bWxfNTA0Yzg2YmU3MzE0NGFhNDg5YTMwOTA5ZGM3ZTM1ZjUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkFtZXJpY2FuIFJlc3RhdXJhbnQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzA5NWNiZDdjMWEwMjQwMmRhYjUzZGRmYTFjYTRlNjlhLnNldENvbnRlbnQoaHRtbF81MDRjODZiZTczMTQ0YWE0ODlhMzA5MDlkYzdlMzVmNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wMWU1ZDg0YTRmODI0YjcxYmI1MDhiNWMwNTY5ZjQzMy5iaW5kUG9wdXAocG9wdXBfMDk1Y2JkN2MxYTAyNDAyZGFiNTNkZGZhMWNhNGU2OWEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMDVmNWEzM2I2NDUyNDNiZGE3NGU0NDJhMjFjOWYzODYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0MC43MTU0ODY1ODUyNDk3MzUsLTc0LjAwOTEzMzEzNTEwODM2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjYsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2Y2Y2I4YWQ1NTFkYjRhYjViOWY4Nzc2ZGRmNGY3MGI4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzFjNGY3YjhhODNkMzRjNzlhNWRmMWYxNzRhNDRkMTgwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2U0ODRlMjM3MjZlYjRjMWFiZTYxMWYxMTEzZjg4NzUzID0gJCgnPGRpdiBpZD0iaHRtbF9lNDg0ZTIzNzI2ZWI0YzFhYmU2MTFmMTExM2Y4ODc1MyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QW1lcmljYW4gUmVzdGF1cmFudDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMWM0ZjdiOGE4M2QzNGM3OWE1ZGYxZjE3NGE0NGQxODAuc2V0Q29udGVudChodG1sX2U0ODRlMjM3MjZlYjRjMWFiZTYxMWYxMTEzZjg4NzUzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzA1ZjVhMzNiNjQ1MjQzYmRhNzRlNDQyYTIxYzlmMzg2LmJpbmRQb3B1cChwb3B1cF8xYzRmN2I4YTgzZDM0Yzc5YTVkZjFmMTc0YTQ0ZDE4MCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yZmQxNTU0NjE2ZWI0YmM0OTNmYmU0NDA2N2RlMWJhZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjcxNzAxMDExNDA5OTA2LC03NC4wMDgwNDI0NDU2MjIyNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC42LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84NjBiZDE0ZDdkZmQ0YzZhOTEyZDM4NWVhZGEyM2Y3MyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wMTBhZDEzMjYwMGM0ODRiODZlN2I0MmZhNjI1Y2IzMSA9ICQoJzxkaXYgaWQ9Imh0bWxfMDEwYWQxMzI2MDBjNDg0Yjg2ZTdiNDJmYTYyNWNiMzEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZyZW5jaCBSZXN0YXVyYW50PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF84NjBiZDE0ZDdkZmQ0YzZhOTEyZDM4NWVhZGEyM2Y3My5zZXRDb250ZW50KGh0bWxfMDEwYWQxMzI2MDBjNDg0Yjg2ZTdiNDJmYTYyNWNiMzEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMmZkMTU1NDYxNmViNGJjNDkzZmJlNDQwNjdkZTFiYWUuYmluZFBvcHVwKHBvcHVwXzg2MGJkMTRkN2RmZDRjNmE5MTJkMzg1ZWFkYTIzZjczKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2FmODY1NDA3ZjBlODRkMTRhMjU4MmRlYWVjMjE5MmM0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuNzE3MTcyNzU4MDExNjgsLTc0LjAwOTMyODY5MTI1MTE3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjYsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2Y2Y2I4YWQ1NTFkYjRhYjViOWY4Nzc2ZGRmNGY3MGI4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzYzODYxYTRiMGQ3MTQ2MTU5NTkwMTFlM2ZjODZmYjM2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzQ0MDFhZjM3YTFiZTRjMzE5N2ZkNDIwNWRkYjMyYmYzID0gJCgnPGRpdiBpZD0iaHRtbF80NDAxYWYzN2ExYmU0YzMxOTdmZDQyMDVkZGIzMmJmMyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UGFyazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjM4NjFhNGIwZDcxNDYxNTk1OTAxMWUzZmM4NmZiMzYuc2V0Q29udGVudChodG1sXzQ0MDFhZjM3YTFiZTRjMzE5N2ZkNDIwNWRkYjMyYmYzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2FmODY1NDA3ZjBlODRkMTRhMjU4MmRlYWVjMjE5MmM0LmJpbmRQb3B1cChwb3B1cF82Mzg2MWE0YjBkNzE0NjE1OTU5MDExZTNmYzg2ZmIzNik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jNDVmZjUwMTYxMTc0NjJlODRkNjlmNDI2NDdjNjQ5YiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjcxNTU4LC03NC4wMDk4NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC42LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zZWY5OWUyN2IyZjg0NzQ2ODRhMzEwMjExNDJkODNiOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lNTRiNzRiZTNjMzM0ODQyYTA5NTk1NmQ2YjM2NzJmMSA9ICQoJzxkaXYgaWQ9Imh0bWxfZTU0Yjc0YmUzYzMzNDg0MmEwOTU5NTZkNmIzNjcyZjEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJhZ2VsIFNob3A8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzNlZjk5ZTI3YjJmODQ3NDY4NGEzMTAyMTE0MmQ4M2I5LnNldENvbnRlbnQoaHRtbF9lNTRiNzRiZTNjMzM0ODQyYTA5NTk1NmQ2YjM2NzJmMSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jNDVmZjUwMTYxMTc0NjJlODRkNjlmNDI2NDdjNjQ5Yi5iaW5kUG9wdXAocG9wdXBfM2VmOTllMjdiMmY4NDc0Njg0YTMxMDIxMTQyZDgzYjkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTI5NjM4OWY3NmEwNDhmYzlhMjYxMzUwMmFjYjcwZDggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0MC43MTU4ODUxMjYwODYwMTUsLTc0LjAwODcxMzYxOTAyNDE4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjYsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2Y2Y2I4YWQ1NTFkYjRhYjViOWY4Nzc2ZGRmNGY3MGI4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzlkNzU5N2ZmZDVlNDQyY2Y5MjM4YjJkZDRiZWRiYjRkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzJiODZjYmUyODI4ODRkN2U5Y2ZhNTNhMWQzMTAyNmNmID0gJCgnPGRpdiBpZD0iaHRtbF8yYjg2Y2JlMjgyODg0ZDdlOWNmYTUzYTFkMzEwMjZjZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q29ja3RhaWwgQmFyPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85ZDc1OTdmZmQ1ZTQ0MmNmOTIzOGIyZGQ0YmVkYmI0ZC5zZXRDb250ZW50KGh0bWxfMmI4NmNiZTI4Mjg4NGQ3ZTljZmE1M2ExZDMxMDI2Y2YpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOTI5NjM4OWY3NmEwNDhmYzlhMjYxMzUwMmFjYjcwZDguYmluZFBvcHVwKHBvcHVwXzlkNzU5N2ZmZDVlNDQyY2Y5MjM4YjJkZDRiZWRiYjRkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzg3YzY3MDI4NTA1MTQ5ZDlhOTgxZTBiMTA1YjczZmI0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuNzE1MTQzOSwtNzQuMDA5MTgyNl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC42LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lODRjM2ZhMWVhOWY0OTJiODExNWIyODlkOWUyZjliMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84YzRkZTBkNDk4ZTQ0MDE3Yjk4YmNmYTZiYzgxNGFjMCA9ICQoJzxkaXYgaWQ9Imh0bWxfOGM0ZGUwZDQ5OGU0NDAxN2I5OGJjZmE2YmM4MTRhYzAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhvdGVsPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lODRjM2ZhMWVhOWY0OTJiODExNWIyODlkOWUyZjliMC5zZXRDb250ZW50KGh0bWxfOGM0ZGUwZDQ5OGU0NDAxN2I5OGJjZmE2YmM4MTRhYzApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfODdjNjcwMjg1MDUxNDlkOWE5ODFlMGIxMDViNzNmYjQuYmluZFBvcHVwKHBvcHVwX2U4NGMzZmExZWE5ZjQ5MmI4MTE1YjI4OWQ5ZTJmOWIwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzEwNjRmMzk5YjNlMjRiNGY4ZDQwM2JiYTQ0ZDkwYTAxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuNzE3MDQ1OTg4NTM3MDQsLTc0LjAxMTA5NDU3MDE1OTkxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjYsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2Y2Y2I4YWQ1NTFkYjRhYjViOWY4Nzc2ZGRmNGY3MGI4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzg5NDZmYThkZDdiZjRlZmE5NjIwOGVkYTI1NjJiMjk1ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzk5NGZjNzc1ZTMwYjRkNTRiNDk2Mzc2NjkxZWYyOWQ4ID0gJCgnPGRpdiBpZD0iaHRtbF85OTRmYzc3NWUzMGI0ZDU0YjQ5NjM3NjY5MWVmMjlkOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UGxheWdyb3VuZDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODk0NmZhOGRkN2JmNGVmYTk2MjA4ZWRhMjU2MmIyOTUuc2V0Q29udGVudChodG1sXzk5NGZjNzc1ZTMwYjRkNTRiNDk2Mzc2NjkxZWYyOWQ4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzEwNjRmMzk5YjNlMjRiNGY4ZDQwM2JiYTQ0ZDkwYTAxLmJpbmRQb3B1cChwb3B1cF84OTQ2ZmE4ZGQ3YmY0ZWZhOTYyMDhlZGEyNTYyYjI5NSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zNmY0NWExZWZiYWY0YzMxOTY2YjQ3ZDI3NGQ1NDcwOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjcxNjgwMjAzMzU3NDEyNiwtNzQuMDEwODc5OTkzNDM4NzJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuNiwKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZjZjYjhhZDU1MWRiNGFiNWI5Zjg3NzZkZGY0ZjcwYjgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNjJlYjE1MDEyYWE5NDk3MDk1NmI5N2U3OTBhYzE2M2IgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODA5NzI3NmYzOTdhNGIxN2E0MTYwNjllMmE4MjU4YTEgPSAkKCc8ZGl2IGlkPSJodG1sXzgwOTcyNzZmMzk3YTRiMTdhNDE2MDY5ZTJhODI1OGExIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5GYXJtZXJzIE1hcmtldDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjJlYjE1MDEyYWE5NDk3MDk1NmI5N2U3OTBhYzE2M2Iuc2V0Q29udGVudChodG1sXzgwOTcyNzZmMzk3YTRiMTdhNDE2MDY5ZTJhODI1OGExKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzM2ZjQ1YTFlZmJhZjRjMzE5NjZiNDdkMjc0ZDU0NzA4LmJpbmRQb3B1cChwb3B1cF82MmViMTUwMTJhYTk0OTcwOTU2Yjk3ZTc5MGFjMTYzYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85MGI3MTI2YTdlOTA0Zjk2YWZjZmI2OWY5MzI1MjAzNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjcxNzM5NDQ1MjkxNjUsLTc0LjAxMDEwMzI0NjA3MTI1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjYsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2Y2Y2I4YWQ1NTFkYjRhYjViOWY4Nzc2ZGRmNGY3MGI4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzRmMjFmYjExYmYzNjQxZjhiODI1ODk1Mzg4YjExNzY1ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2ZjNjRkYWU5YzFlYTQ0MTU5NTUwNmNmMTU1YmE3Y2Y2ID0gJCgnPGRpdiBpZD0iaHRtbF9mYzY0ZGFlOWMxZWE0NDE1OTU1MDZjZjE1NWJhN2NmNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q29mZmVlIFNob3A8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzRmMjFmYjExYmYzNjQxZjhiODI1ODk1Mzg4YjExNzY1LnNldENvbnRlbnQoaHRtbF9mYzY0ZGFlOWMxZWE0NDE1OTU1MDZjZjE1NWJhN2NmNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85MGI3MTI2YTdlOTA0Zjk2YWZjZmI2OWY5MzI1MjAzNS5iaW5kUG9wdXAocG9wdXBfNGYyMWZiMTFiZjM2NDFmOGI4MjU4OTUzODhiMTE3NjUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNTYyNzg2YTBiYjMzNDEyOWEzYzgyNTgzODQ0MTBmMWUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0MC43MTY4MzIsLTc0LjAwODQ2OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC42LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84ZjI4NTM3MTJhNzI0MTEwOWNjNjEyNGY3ZTYwN2IwMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kOGRkOGMxZTA4MWI0NzEyYTA3ZjA0MGJiYTc0YTlhMiA9ICQoJzxkaXYgaWQ9Imh0bWxfZDhkZDhjMWUwODFiNDcxMmEwN2YwNDBiYmE3NGE5YTIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkl0YWxpYW4gUmVzdGF1cmFudDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOGYyODUzNzEyYTcyNDExMDljYzYxMjRmN2U2MDdiMDAuc2V0Q29udGVudChodG1sX2Q4ZGQ4YzFlMDgxYjQ3MTJhMDdmMDQwYmJhNzRhOWEyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzU2Mjc4NmEwYmIzMzQxMjlhM2M4MjU4Mzg0NDEwZjFlLmJpbmRQb3B1cChwb3B1cF84ZjI4NTM3MTJhNzI0MTEwOWNjNjEyNGY3ZTYwN2IwMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82YTZhNjJmZTMwYWQ0MmI5OTYxZmU5M2U1MzkwZWM3MyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjcxMjc1MjUxNzcxNDg1LC03NC4wMDg3MzM1NTYwMTU3MV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC42LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9mNmNiOGFkNTUxZGI0YWI1YjlmODc3NmRkZjRmNzBiOCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83MmQwZDBhNzlhMDI0ZGM1OTE2NTAxZDAyYjllYjExYiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85Mzg1YWYyMDA0NGY0YmJiYjIyYmQyNWY5YjA5ZDU1MiA9ICQoJzxkaXYgaWQ9Imh0bWxfOTM4NWFmMjAwNDRmNGJiYmIyMmJkMjVmOWIwOWQ1NTIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkd5bSAvIEZpdG5lc3MgQ2VudGVyPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83MmQwZDBhNzlhMDI0ZGM1OTE2NTAxZDAyYjllYjExYi5zZXRDb250ZW50KGh0bWxfOTM4NWFmMjAwNDRmNGJiYmIyMmJkMjVmOWIwOWQ1NTIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNmE2YTYyZmUzMGFkNDJiOTk2MWZlOTNlNTM5MGVjNzMuYmluZFBvcHVwKHBvcHVwXzcyZDBkMGE3OWEwMjRkYzU5MTY1MDFkMDJiOWViMTFiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2Q5YzJlMDg0MmVkZjQ0ZjFhNjZiYTczNGU3Mjk4YjE3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuNzE1MDQ1MTI1NTg5OTYsLTc0LjAxMTUwODcxMDI4MjFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuNiwKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZjZjYjhhZDU1MWRiNGFiNWI5Zjg3NzZkZGY0ZjcwYjgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDE5YWNkMmJhZDgzNGVmYTlhODMxZjZmMzlhNDdlNmEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjQxNzUzOGY3NDgzNGI2NTk2NjQwYTUxZTc0MGJiODQgPSAkKCc8ZGl2IGlkPSJodG1sXzI0MTc1MzhmNzQ4MzRiNjU5NjY0MGE1MWU3NDBiYjg0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Db2ZmZWUgU2hvcDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDE5YWNkMmJhZDgzNGVmYTlhODMxZjZmMzlhNDdlNmEuc2V0Q29udGVudChodG1sXzI0MTc1MzhmNzQ4MzRiNjU5NjY0MGE1MWU3NDBiYjg0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Q5YzJlMDg0MmVkZjQ0ZjFhNjZiYTczNGU3Mjk4YjE3LmJpbmRQb3B1cChwb3B1cF9kMTlhY2QyYmFkODM0ZWZhOWE4MzFmNmYzOWE0N2U2YSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81YWMyYjllZjVlMDk0ZjIzOThhNzE5YmFjODMxNTU5MSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjcxNDUzNzQzMTE3MDQ4NCwtNzQuMDA1OTk4NTI2MTE1OTJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuNiwKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZjZjYjhhZDU1MWRiNGFiNWI5Zjg3NzZkZGY0ZjcwYjgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDI3MDc4NmUzYWM2NGRhNmE4YjJlNmFkZWQ0ZGI1NTYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDRkNmE1NjM4MTQwNGM3YjgzN2Y2YWFiZTg3ZmViNWYgPSAkKCc8ZGl2IGlkPSJodG1sX2Q0ZDZhNTYzODE0MDRjN2I4MzdmNmFhYmU4N2ZlYjVmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5HeW08L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2QyNzA3ODZlM2FjNjRkYTZhOGIyZTZhZGVkNGRiNTU2LnNldENvbnRlbnQoaHRtbF9kNGQ2YTU2MzgxNDA0YzdiODM3ZjZhYWJlODdmZWI1Zik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81YWMyYjllZjVlMDk0ZjIzOThhNzE5YmFjODMxNTU5MS5iaW5kUG9wdXAocG9wdXBfZDI3MDc4NmUzYWM2NGRhNmE4YjJlNmFkZWQ0ZGI1NTYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZDk4MjUwN2JjMzBjNDllNGE3NGM4Mjk3YWMyNzU1MTggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0MC43MTI2MTI0NjUwMTk4MywtNzQuMDA5MzgwMzIwODc2MjhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuNiwKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZjZjYjhhZDU1MWRiNGFiNWI5Zjg3NzZkZGY0ZjcwYjgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNDg3OTFhNDA2ZjczNDkxZDkzZjRhNjcyZWJkZGNkM2IgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMDJlY2M1MmU5NjA4NDdlYjlhZGVjN2I5NjAzNzgxYmUgPSAkKCc8ZGl2IGlkPSJodG1sXzAyZWNjNTJlOTYwODQ3ZWI5YWRlYzdiOTYwMzc4MWJlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Ib3RlbDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNDg3OTFhNDA2ZjczNDkxZDkzZjRhNjcyZWJkZGNkM2Iuc2V0Q29udGVudChodG1sXzAyZWNjNTJlOTYwODQ3ZWI5YWRlYzdiOTYwMzc4MWJlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Q5ODI1MDdiYzMwYzQ5ZTRhNzRjODI5N2FjMjc1NTE4LmJpbmRQb3B1cChwb3B1cF80ODc5MWE0MDZmNzM0OTFkOTNmNGE2NzJlYmRkY2QzYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zNWNkYzQyMTcwM2Y0ZDQ0YTI2ZThmZWUwNTlmZWZmMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjcxNTYwNTMzMzE0NzE0NSwtNzQuMDExNzg2MzMyMDg3NzFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuNiwKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZjZjYjhhZDU1MWRiNGFiNWI5Zjg3NzZkZGY0ZjcwYjgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfY2JiYzI3NTE0MjZiNDJkZWE0MzFjNzUzMTJkZGNlOTkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNWUzNGY1YjI0N2I2NDM2MDg4MGRmNjU0YTI1ODVjNTAgPSAkKCc8ZGl2IGlkPSJodG1sXzVlMzRmNWIyNDdiNjQzNjA4ODBkZjY1NGEyNTg1YzUwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Cb29rc3RvcmU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2NiYmMyNzUxNDI2YjQyZGVhNDMxYzc1MzEyZGRjZTk5LnNldENvbnRlbnQoaHRtbF81ZTM0ZjViMjQ3YjY0MzYwODgwZGY2NTRhMjU4NWM1MCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zNWNkYzQyMTcwM2Y0ZDQ0YTI2ZThmZWUwNTlmZWZmMi5iaW5kUG9wdXAocG9wdXBfY2JiYzI3NTE0MjZiNDJkZWE0MzFjNzUzMTJkZGNlOTkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNjRjNzMyZTI3YmI2NDU5NGEyNGI4NjBjN2NhMmQ5OWIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0MC43MTY3NTE3NjEzMDcyNCwtNzQuMDA1NzExNTA4NTU0NTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuNiwKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZjZjYjhhZDU1MWRiNGFiNWI5Zjg3NzZkZGY0ZjcwYjgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNmY1ZjZjZjBlNTQzNGZiNmIxM2NjNzRmYTYwOGM2YTIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYjJmM2U3NmRkYWI3NGE3OGIwMjNhYjEyZmIyMTUxZTkgPSAkKCc8ZGl2IGlkPSJodG1sX2IyZjNlNzZkZGFiNzRhNzhiMDIzYWIxMmZiMjE1MWU5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Nb2xlY3VsYXIgR2FzdHJvbm9teSBSZXN0YXVyYW50PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82ZjVmNmNmMGU1NDM0ZmI2YjEzY2M3NGZhNjA4YzZhMi5zZXRDb250ZW50KGh0bWxfYjJmM2U3NmRkYWI3NGE3OGIwMjNhYjEyZmIyMTUxZTkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNjRjNzMyZTI3YmI2NDU5NGEyNGI4NjBjN2NhMmQ5OWIuYmluZFBvcHVwKHBvcHVwXzZmNWY2Y2YwZTU0MzRmYjZiMTNjYzc0ZmE2MDhjNmEyKTsKCiAgICAgICAgICAgIAogICAgICAgIAo8L3NjcmlwdD4= onload="this.contentDocument.open();this.contentDocument.write(atob(this.getAttribute('data-html')));this.contentDocument.close();" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



   

<a id="item5"></a>

## 5. Explore Trending Venues
> `https://api.foursquare.com/v2/venues/`**trending**`?client_id=`**CLIENT_ID**`&client_secret=`**CLIENT_SECRET**`&ll=`**LATITUDE**`,`**LONGITUDE**`&v=`**VERSION**

#### Now, instead of simply exploring the area around Ecco, you are interested in knowing the venues that are trending at the time you are done with your lunch, meaning the places with the highest foot traffic. So let's do that and get the trending venues around Ecco.


```python
# define URL
url = 'https://api.foursquare.com/v2/venues/trending?client_id={}&client_secret={}&ll={},{}&v={}'.format(CLIENT_ID, CLIENT_SECRET, latitude, longitude, VERSION)

# send GET request and get trending venues
results = requests.get(url).json()
results
```




    {'meta': {'code': 200, 'requestId': '5f1ec88161022762215e71f0'},
     'response': {'venues': []}}



### Check if any venues are trending at this time


```python
if len(results['response']['venues']) == 0:
    trending_venues_df = 'No trending venues are available at the moment!'
    
else:
    trending_venues = results['response']['venues']
    trending_venues_df = json_normalize(trending_venues)

    # filter columns
    columns_filtered = ['name', 'categories'] + ['location.distance', 'location.city', 'location.postalCode', 'location.state', 'location.country', 'location.lat', 'location.lng']
    trending_venues_df = trending_venues_df.loc[:, columns_filtered]

    # filter the category for each row
    trending_venues_df['categories'] = trending_venues_df.apply(get_category_type, axis=1)
```


```python
# display trending venues
trending_venues_df
```




    'No trending venues are available at the moment!'



Now, depending on when you run the above code, you might get different venues since the venues with the highest foot traffic are fetched live. 

### Visualize trending venues


```python
if len(results['response']['venues']) == 0:
    venues_map = 'Cannot generate visual as no trending venues are available at the moment!'

else:
    venues_map = folium.Map(location=[latitude, longitude], zoom_start=15) # generate map centred around Ecco


    # add Ecco as a red circle mark
    folium.features.CircleMarker(
        [latitude, longitude],
        radius=10,
        popup='Ecco',
        fill=True,
        color='red',
        fill_color='red',
        fill_opacity=0.6
    ).add_to(venues_map)


    # add the trending venues as blue circle markers
    for lat, lng, label in zip(trending_venues_df['location.lat'], trending_venues_df['location.lng'], trending_venues_df['name']):
        folium.features.CircleMarker(
            [lat, lng],
            radius=5,
            poup=label,
            fill=True,
            color='blue',
            fill_color='blue',
            fill_opacity=0.6
        ).add_to(venues_map)
```


```python
# display map
venues_map
```




    'Cannot generate visual as no trending venues are available at the moment!'



<a id="item6"></a>

   

### Thank you for completing this lab!

This notebook was created by [Alex Aklson](https://www.linkedin.com/in/aklson/). I hope you found this lab interesting and educational. Feel free to contact me if you have any questions!

This notebook is part of a course on **Coursera** called *Applied Data Science Capstone*. If you accessed this notebook outside the course, you can take this course online by clicking [here](http://cocl.us/DP0701EN_Coursera_Week2_LAB1).

<hr>
Copyright &copy; 2018 [Cognitive Class](https://cognitiveclass.ai/?utm_source=bducopyrightlink&utm_medium=dswb&utm_campaign=bdu). This notebook and its source code are released under the terms of the [MIT License](https://bigdatauniversity.com/mit-license/).
