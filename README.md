# ExampleUseage

## Authentication

you will need the api key available from the main web interface of zentracloud (just click the api button in the sidebar and copy the token to your clipboard)

in the following example we will get the latest reading from the given serial number 

**example.py**
```python
#! /usr/bin/python

import requests
# make sure your token is of the form 'TOKEN ae44fa...' !
TOKEN = 'TOKEN <YOUR_API_TOKEN>'
DEVICE_SN = 'z6-<SERIAL>'

#authentication header
headers = {'Authorization':TOKEN}

# just get the last reading
params = {'per_page':1, 'device_sn': DEVICE_SN, 'output_format': 'json'}

response = requests.get('https://zentracloud.com/api/v3/get_readings',params=params,headers=headers)
data = response.json()
# information
pagination_data = data['pagination']
print('Pagination Data:',pagination_data)
battery_data = data['data']['Battery Percent'][0] # most recent config
print("Latest Battery Percent:",battery_data['readings'][0])
print("Latest Battery MetaData:",battery_data['metadata'])
```

### Working with reading data

It is recommended that you install the pandas package if you want to work with reading data provided by the api

```python
#! /usr/bin/python

import requests
import pandas
# make sure your token is of the form 'TOKEN ae44fa...' !
TOKEN = 'TOKEN <YOUR_API_TOKEN>'
DEVICE_SN = 'z6-<SERIAL>'

#authentication header
headers = {'Authorization':TOKEN}

# get the last 2000 readings
params = {'per_page':2000, 'device_sn': DEVICE_SN, 'output_format': 'df'}

response = requests.get('https://zentracloud.com/api/v3/get_readings', params=params, headers=headers)
data = response.json()

dataframe_data = json.loads(data['data']['readings']) # it is a string encoded json payload so you must decode it again 
df = pandas.DataFrame(**dataframe_data)
```
