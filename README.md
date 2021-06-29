# ExampleUseage

## Authentication

you will need the api key available from the main web interface of zentracloud (just click the api button in the sidebar and copy the token to your clipboard)

in the following example we will get the latest reading from the given serial number 


**example.py**
```python
#! /usr/bin/python

import requests # use `pip install requests` to install
# make sure your token is of the form 'TOKEN ae44fa...' !
TOKEN = 'TOKEN <YOUR_API_TOKEN>'
DEVICE_SN = 'z6-<SERIAL>'

#authentication header
headers = {'Authorization':TOKEN}

# just get the last reading
params = {'per_page':1, 'device_sn': DEVICE_SN, 'output_format': 'json'}

response = requests.get('https://zentracloud.com/api/v3/get_readings',params=params,headers=headers)
data = response.json()
# information about this "page" of response data
pagination_data = data['pagination']
print('Pagination Data:',pagination_data)

# get some 
battery_data = data['data']['Battery Percent'][0] 
print("Latest Battery Percent:",battery_data['readings'][0])
print("Latest Battery MetaData:",battery_data['metadata'])
```

### Working with reading data

It is recommended that you install the pandas package if you want to work with reading data provided by the api

```python
#! /usr/bin/python

import requests  # use `pip install requests` to install
import pandas   # use `pip install pandas` to install
# make sure your token is of the form 'TOKEN ae44fa...' !
TOKEN = 'TOKEN <YOUR_API_TOKEN>'
DEVICE_SN = 'z6-<SERIAL>'

#authentication header
headers = {'Authorization':TOKEN}

# get the last 2000 readings
params = {'per_page':2000, 'device_sn': DEVICE_SN, 'output_format': 'df'}

response = requests.get('https://zentracloud.com/api/v3/get_readings', params=params, headers=headers)
data = response.json()

dataframe_data = json.loads(data['data']) # it is a string encoded json payload so you must decode it again 
print(df) # now that our data is in a dataframe it is much easier to work with

#lets select our battery readings
battery_readings_mask = df['measurement']=='Battery Percent'
battery_readings = df[battery_readings_mask].copy()
```

### convert our index to datetimes and plot the battery data

```python
import matplotlib  # use `pip install matplotlib` to install
...
battery_readings = df[battery_readings_mask].copy()
datetimes = pandas.to_datetime( battery_readings['timestamp_utc'], unit='s')

battery_readings = battery_readings.set_index( datetimes )
#resample the data
resampled_data = battery_readings.resample('1h')['value'].agg('mean')
battery_readings.plot()
pyplot.show()
```
