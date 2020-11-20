
# Collecting data from the web: handy codes

## 1. Very plain very vanilla code

This two-line python code will fetch content from a given valid url!


```python
import requests # As you can guess, this 'requests' module is for making a request to a web page
r = requests.get("https://openweathermap.org/") 


# Let's check what type of object we get back from the web server by the code above:

print(type(r), '\n')

# Now let's check a small part of that response object:

n=0

for line in r:
    print(line)
    if n>10:
        break
    n+=1

```

    <class 'requests.models.Response'> 
    
    b'<!DOCTYPE html>\n<html lang=\'en\'>\n    <head>\n        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">    \n    '
    b'<meta  http-equiv=Expires content="Tue, Sep 20 2018 15:27:22 GMT">\n    <meta http-equiv="Last-Modified" content="Tue, Sep 20 201'
    b'8 15:27:22 GMT">\n    <!--<meta http-equiv="X-UA-Compatible" content="IE=edge">-->\n    <meta name="viewport" content="width=devic'
    b'e-width, initial-scale=1.0">\n    <meta name="keywords" content="weather, world, Openstreetmap, forecast, Openweather, current" >'
    b'\n    <meta name="description" content="Get current weather, hourly forecast, daily forecast for 16 days, and 3-hourly forecast 5'
    b' days for your city. Historical weather data for 40 years back for any coordinate. Helpful stats, graphics, and this day in hist'
    b'ory charts are available for your reference. Interactive maps show precipitation, clouds, pressure, wind around your location." '
    b'>\n    <meta name="author" content="OpenWeatherMap.org">\n    <meta name="domain" content="OpenWeatherMap.org" >        \n    <meta'
    b' name="p:domain_verify" content="34fe229eab8562dca90f4a5962ff80a9"/>\n    <meta property="title" content="\xd0\xa1urrent weather and fo'
    b'recast" />                <link rel="shortcut icon" href="/themes/openweathermap/assets/vendor/owm/img/icons/favicon.ico" />\n<li'
    b'nk rel="icon" href="/themes/openweathermap/assets/vendor/owm/img/icons/logo_16x16.png" sizes="16x16" type="image/png">\n<link rel'
    b'="icon" href="/themes/openweathermap/assets/vendor/owm/img/icons/logo_32x32.png" sizes="32x32" type="image/png">\n<link rel="icon'
    

### Headers & Payload
Each HTTP message has a header and payload
The header contains metadata about the request.
The Payload contains the data in the request.
The header is like the outside of an envelope.  It contains a to and from address.
It also may contain information or clues as to the type of mail it is.

The payload contains the actual correspondence or contents.  This pertains to the business being conducted.


```python
print(r.headers) # Checking the header of a response object
```

    {'Server': 'openresty/1.9.7.1', 'Date': 'Fri, 20 Nov 2020 21:27:46 GMT', 'Content-Type': 'text/html; charset=UTF-8', 'Content-Length': '7997', 'Connection': 'keep-alive', 'X-Powered-By': 'PHP/7.1.7', 'Cache-Control': 'private, must-revalidate', 'pragma': 'no-cache', 'expires': '-1', 'Set-Cookie': 'october_session=eyJpdiI6IkVHWFRNQXVaTURwbllaNkFXQmZZeXc9PSIsInZhbHVlIjoiOVpoRUhQOGZPdjdESUhxZ2VWaHpnd0RcL0w1Q1ZYbHRJOEtLc3NXMTVRYVFsblFWV24ycGdmWHM0VnNLbkx3WlwvbW5zWEtBSjVha05cLzNGNEthQlJiYVE9PSIsIm1hYyI6IjhhMjcwZDFhNTA0MTc5OTYwODEyYTJiOWQxYzNiYzY2ZjlmOTU3NzZhZDYzN2JmZTU1ZjM5ODljOWY2NGQ4NzEifQ%3D%3D; expires=Fri, 20-Nov-2020 23:27:46 GMT; Max-Age=7200; path=/; HttpOnly', 'Vary': 'Accept-Encoding', 'Content-Encoding': 'gzip'}
    

## 2. (slightly) improved codes

Adding just one more line would make the content (which is now a 'requests.models.Response' object, as we checked earlier) much more palatable.

With the string object that we get by the code below, we can do some 'detective' works to find the content of our interest, and fetch that content with indexing/slicing operations


```python
import requests
r = requests.get("https://openweathermap.org/")
print(r.text[0:2500], '\n') 

# Now let's check the type of this r.text object:

print(type(r.text)) 
```

    <!DOCTYPE html>
    <html lang='en'>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=utf-8">    
        <meta  http-equiv=Expires content="Tue, Sep 20 2018 15:27:22 GMT">
        <meta http-equiv="Last-Modified" content="Tue, Sep 20 2018 15:27:22 GMT">
        <!--<meta http-equiv="X-UA-Compatible" content="IE=edge">-->
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta name="keywords" content="weather, world, Openstreetmap, forecast, Openweather, current" >
        <meta name="description" content="Get current weather, hourly forecast, daily forecast for 16 days, and 3-hourly forecast 5 days for your city. Historical weather data for 40 years back for any coordinate. Helpful stats, graphics, and this day in history charts are available for your reference. Interactive maps show precipitation, clouds, pressure, wind around your location." >
        <meta name="author" content="OpenWeatherMap.org">
        <meta name="domain" content="OpenWeatherMap.org" >        
        <meta name="p:domain_verify" content="34fe229eab8562dca90f4a5962ff80a9"/>
        <meta property="title" content="Сurrent weather and forecast" />                <link rel="shortcut icon" href="/themes/openweathermap/assets/vendor/owm/img/icons/favicon.ico" />
    <link rel="icon" href="/themes/openweathermap/assets/vendor/owm/img/icons/logo_16x16.png" sizes="16x16" type="image/png">
    <link rel="icon" href="/themes/openweathermap/assets/vendor/owm/img/icons/logo_32x32.png" sizes="32x32" type="image/png">
    <link rel="icon" href="/themes/openweathermap/assets/vendor/owm/img/icons/logo_60x60.png" sizes="60x60" type="image/png">   
            <title>Сurrent weather and forecast - OpenWeatherMap</title>
            <!-- Global site tag (gtag.js) - Google Analytics -->
    <script async src="https://www.googletagmanager.com/gtag/js?id=UA-31601618-1"></script>
    <script>
      window.dataLayer = window.dataLayer || [];
      function gtag(){dataLayer.push(arguments);}
      gtag('js', new Date());
    
      gtag('config', 'UA-31601618-1');
    </script>        
            <script src="//code.jquery.com/jquery-1.9.1.min.js" integrity="sha256-wS9gmOZBqsqWxgIVgA8Y9WcQOa7PgSIX+rPA0VL2rbQ=" crossorigin="anonymous"></script>
    <script src="https://cdn.polyfill.io/v2/polyfill.min.js?features=Array.from,Array.assign,fetch&flags=always,gated"></script>
    <script src='/themes/openweathermap/assets/js/bundle_owm_preload.c31872127471c475ade1.js'></script>            
            <link href='/themes/openweathermap/assets/css/bundle_ 
    
    <class 'str'>
    

## 3.  API requests

Usage of RESTful APIs for data analysts


```python
# The url used below provides an example of the response to an API request (as a dummy of an actual API request)
# Note the difference in the responses:
# The previous three responses are human readable(when rendered by a browser), and this one is machine readable

url = "http://dummy.restapiexample.com/api/v1/employees"

api_r1 = requests.get(url)
print(api_r1.text)
```

    {"status":"success","data":[{"id":"1","employee_name":"Tiger Nixon","employee_salary":"320800","employee_age":"61","profile_image":""},{"id":"2","employee_name":"Garrett Winters","employee_salary":"170750","employee_age":"63","profile_image":""},{"id":"3","employee_name":"Ashton Cox","employee_salary":"86000","employee_age":"66","profile_image":""},{"id":"4","employee_name":"Cedric Kelly","employee_salary":"433060","employee_age":"22","profile_image":""},{"id":"5","employee_name":"Airi Satou","employee_salary":"162700","employee_age":"33","profile_image":""},{"id":"6","employee_name":"Brielle Williamson","employee_salary":"372000","employee_age":"61","profile_image":""},{"id":"7","employee_name":"Herrod Chandler","employee_salary":"137500","employee_age":"59","profile_image":""},{"id":"8","employee_name":"Rhona Davidson","employee_salary":"327900","employee_age":"55","profile_image":""},{"id":"9","employee_name":"Colleen Hurst","employee_salary":"205500","employee_age":"39","profile_image":""},{"id":"10","employee_name":"Sonya Frost","employee_salary":"103600","employee_age":"23","profile_image":""},{"id":"11","employee_name":"Jena Gaines","employee_salary":"90560","employee_age":"30","profile_image":""},{"id":"12","employee_name":"Quinn Flynn","employee_salary":"342000","employee_age":"22","profile_image":""},{"id":"13","employee_name":"Charde Marshall","employee_salary":"470600","employee_age":"36","profile_image":""},{"id":"14","employee_name":"Haley Kennedy","employee_salary":"313500","employee_age":"43","profile_image":""},{"id":"15","employee_name":"Tatyana Fitzpatrick","employee_salary":"385750","employee_age":"19","profile_image":""},{"id":"16","employee_name":"Michael Silva","employee_salary":"198500","employee_age":"66","profile_image":""},{"id":"17","employee_name":"Paul Byrd","employee_salary":"725000","employee_age":"64","profile_image":""},{"id":"18","employee_name":"Gloria Little","employee_salary":"237500","employee_age":"59","profile_image":""},{"id":"19","employee_name":"Bradley Greer","employee_salary":"132000","employee_age":"41","profile_image":""},{"id":"20","employee_name":"Dai Rios","employee_salary":"217500","employee_age":"35","profile_image":""},{"id":"21","employee_name":"Jenette Caldwell","employee_salary":"345000","employee_age":"30","profile_image":""},{"id":"22","employee_name":"Yuri Berry","employee_salary":"675000","employee_age":"40","profile_image":""},{"id":"23","employee_name":"Caesar Vance","employee_salary":"106450","employee_age":"21","profile_image":""},{"id":"24","employee_name":"Doris Wilder","employee_salary":"85600","employee_age":"23","profile_image":""}]}
    


```python
# Example of true API request (not a dummy as in the previous example)
# Yo'll need to collect ( free of cost :-) ) an API key from https://openweathermap.org/api
# You can try your own ZIP instead of mine: just replace 13367 with yours in the url below

url = "https://api.openweathermap.org/data/2.5/weather?zip="+str(13676)+"CollectYourFreeAPI&PasteHere" 

api_r2 = requests .get(url)
print(api_r2.text)

# Now We can process the data (the response) programatically by python
# This is powerful, because we can write scripts to programatically access other websites (with the APIs they provides), store the data and analyze
# For example, if we populate  a csv containing 10,000 zip codes, we can write a script to programatically fetch weather data of those 10,000 places and parse them into another csv for conducting any analysis or making visualizations
```

    {"coord":{"lon":-74.97,"lat":44.66},"weather":[{"id":800,"main":"Clear","description":"clear sky","icon":"01d"}],"base":"stations","main":{"temp":279.74,"feels_like":274.12,"temp_min":279.26,"temp_max":280.15,"pressure":1029,"humidity":39},"visibility":10000,"wind":{"speed":4.1,"deg":200},"clouds":{"all":1},"dt":1604174792,"sys":{"type":1,"id":5420,"country":"US","sunrise":1604144213,"sunset":1604180996},"timezone":-14400,"id":0,"name":"Potsdam","cod":200}
    

## 4.  Parsing the weather api response into a CSV

### 4a. Step-1: Converting the content into a python dictionary via json.loads() 

We would use the json.loads() function of the built-in json package of *python*.
This is a **very handy tool** for converting string documents into python dictionaries!


```python
import requests, json, csv
url = "https://api.openweathermap.org/data/2.5/weather?zip="+str(13676)+"CollectYourFreeAPI&PasteHere" # Yo'll need to collect an API key from https://openweathermap.org/api

api_13676 = requests.get(url) 
api_13676_text = api_13676.text # First, converting the response object to string

converted_response = json.loads(api_13676_text) # Now converting the string into a dictionary by jason.loads() function

print(converted_response)

```

    {'coord': {'lon': -74.97, 'lat': 44.66}, 'weather': [{'id': 804, 'main': 'Clouds', 'description': 'overcast clouds', 'icon': '04d'}], 'base': 'stations', 'main': {'temp': 288.88, 'feels_like': 281.55, 'temp_min': 288.15, 'temp_max': 289.15, 'pressure': 1017, 'humidity': 60}, 'visibility': 10000, 'wind': {'speed': 9.8, 'deg': 220, 'gust': 14.4}, 'clouds': {'all': 90}, 'dt': 1605906341, 'sys': {'type': 1, 'id': 5420, 'country': 'US', 'sunrise': 1605873844, 'sunset': 1605907654}, 'timezone': -18000, 'id': 0, 'name': 'Potsdam', 'cod': 200}
    


```python
print(type(converted_response)) # Making sure that the response did convert to a python dictionary!
```

    <class 'dict'>
    


```python
print(converted_response.keys()) # Getting the keys of the dictionary
```

    dict_keys(['coord', 'weather', 'base', 'main', 'visibility', 'wind', 'clouds', 'dt', 'sys', 'timezone', 'id', 'name', 'cod'])
    

### 4b. Step-2: Parsing the python dictionary into a CSV file


```python
import csv
wo = open("weather_output.csv", "w", newline ='')

writer = csv.writer(wo)
for key, value in converted_response.items():
    writer.writerow([key, value])

wo.close()
```

### 4c. Step-3: Checking contents of the new  CSV file


```python
with open('weather_output.csv', 'r') as f:
    reader = csv.reader(f)
    for line in reader:
        print(line)
```

    ['coord', "{'lon': -74.97, 'lat': 44.66}"]
    ['weather', "[{'id': 804, 'main': 'Clouds', 'description': 'overcast clouds', 'icon': '04d'}]"]
    ['base', 'stations']
    ['main', "{'temp': 288.88, 'feels_like': 281.55, 'temp_min': 288.15, 'temp_max': 289.15, 'pressure': 1017, 'humidity': 60}"]
    ['visibility', '10000']
    ['wind', "{'speed': 9.8, 'deg': 220, 'gust': 14.4}"]
    ['clouds', "{'all': 90}"]
    ['dt', '1605906341']
    ['sys', "{'type': 1, 'id': 5420, 'country': 'US', 'sunrise': 1605873844, 'sunset': 1605907654}"]
    ['timezone', '-18000']
    ['id', '0']
    ['name', 'Potsdam']
    ['cod', '200']
    

## 5. Getting required data elements from the converted response

Some code examples are given below to show how to get your required data elements by dictionary keys once you convert the api response into a python dictionary


```python
t= converted_response['main']['temp']

print("It is " +str(t)+"degrees")
```

    It is 288.88degrees
    


```python
print(converted_response['timezone'])
```

    -18000
    


```python
c = converted_response['weather'][0]['description']

print("It is "+str(c) )
```

    It is overcast clouds
    


```python
w = converted_response['coord']

print("City: ", converted_response['name'])
print("Longitude :" +str(w['lon']), '\n', "Latitude: "+ str(w['lat']))
```

    City:  Potsdam
    Longitude :-74.97 
     Latitude: 44.66
    

## 6. Another web-scrapping example

Below you'd find some typical operations needed to be done to ge your required data in a desired format! 

Please view the page source as well as inspect element t fully understand the dectective works done in the codes!

### 6a. Fetching only the desired elements


```python
# Fetching NY state population data: county by county!

import requests
url = 'https://www.newyork-demographics.com/counties_by_population'

r = requests.get(url)
text =r.text
county_names=[] # Here I'll append the county names as given in the website
county_populations=[] # And here the population of each county will be appended
data = text.split("\n")
n=0
for line in data:
        if '-demographics">'in line: # This is an example of some 'detective' works needed to fetch required data
            county_names.append(line.split('">')[1].replace('</a>',''))
        elif ',' in line:
            if '      ' in line:
                county_populations.append(line.replace(' ', ''))# Removing all extra spaces 
                    
print(county_names, '\n')
print(county_populations)
```

    ['Kings County', 'Queens County', 'New York County', 'Suffolk County', 'Bronx County', 'Nassau County', 'Westchester County', 'Erie County', 'Monroe County', 'Richmond County', 'Onondaga County', 'Orange County', 'Rockland County', 'Albany County', 'Dutchess County', 'Saratoga County', 'Oneida County', 'Niagara County', 'Broome County', 'Ulster County', 'Rensselaer County', 'Schenectady County', 'Chautauqua County', 'Oswego County', 'Jefferson County', 'Ontario County', 'St. Lawrence County', 'Tompkins County', 'Putnam County', 'Steuben County', 'Wayne County', 'Chemung County', 'Clinton County', 'Cayuga County', 'Cattaraugus County', 'Sullivan County', 'Madison County', 'Warren County', 'Livingston County', 'Herkimer County', 'Washington County', 'Otsego County', 'Columbia County', 'Genesee County', 'Fulton County', 'Franklin County', 'Montgomery County', 'Tioga County', 'Cortland County', 'Chenango County', 'Greene County', 'Allegany County', 'Delaware County', 'Orleans County', 'Wyoming County', 'Essex County', 'Seneca County', 'Schoharie County', 'Lewis County', 'Yates County', 'Schuyler County', 'Hamilton County'] 
    
    ['2,559,903', '2,253,858', '1,628,706', '1,476,601', '1,418,207', '1,356,924', '967,506', '918,702', '741,770', '476,143', '460,528', '384,940', '325,789', '305,506', '294,218', '229,863', '228,671', '209,281', '190,488', '177,573', '158,714', '155,299', '126,903', '117,124', '109,834', '109,777', '107,740', '102,180', '98,320', '95,379', '89,918', '83,456', '80,485', '76,576', '76,117', '75,432', '70,941', '63,944', '62,914', '61,319', '61,204', '59,493', '59,461', '57,280', '53,383', '50,022', '49,221', '48,203', '47,581', '47,207', '47,188', '46,091', '44,135', '40,352', '39,859', '36,885', '34,016', '30,999', '26,296', '24,913', '17,807', '4,416']
    

### 6b. Making a dictionary with county names as keys and populations as values


```python
population_dict = {county_names[i]: county_populations[i] for i in range(len(county_names))} 
```

### 6c. Creating a csv file containing population data


```python
import csv
population_dict = {county_names[i]: county_populations[i] for i in range(len(county_names))} 
header = ["county", "population"]

with open('population_data.csv', 'w', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(header) 
    for k,v in population_dict.items():
        writer.writerow([k,v])
```

### 6c. Checking the output CSV with the required population data


```python
n=0
with open ('population_data.csv', 'r') as f:
    reader = csv.reader(f)
    
    for row in reader:
        print(row)
        if n>5:
            break
        n+=1
```

    ['county', 'population']
    ['Kings County', '2,559,903']
    ['Queens County', '2,253,858']
    ['New York County', '1,628,706']
    ['Suffolk County', '1,476,601']
    ['Bronx County', '1,418,207']
    ['Nassau County', '1,356,924']
    
