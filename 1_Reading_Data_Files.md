
 # Reading data files: In search for memory* efficient ways!
  
 In most cases, the initial step of data analysis is to get an overall idea about the content of a given data file. Here some basic techniques of opening and reading files are described with examples
 
######  (*Both comuter's and the data analyst's memories)

  ## (A) Raw python


```python
# Opening a file named 'access_subset.log'by read() method and printing its content:
# The read() method returns the specified number of bytes from the file. Default is -1 which means the whole file

with open('access_subset.log') as f:
    content = f.read()
    print(content)

'''
The following slightly different codes will also give similar output:-

with open('access_subset.log') as f:
    content = f.read()
    lines = content.split("\n")
    print(content)
 ..................   
f = open('access_subset.log')
content = f.read()
lines = content.split("\n")
f.close()
print(lines)
'''

# ! This would print the whole content of the file.
# So, trying this method only to have an overview might not be a good idea 


```


```python
# Reading lines into a list of lists and then iterating over it:

with open('access_subset.log') as f:
    content = f.read()
    lines = content.split("\n")

    n=0 #counter to limit print content
    for line in lines:
        print(line)
        if n==3:
            break
        n+=1
# This is a better approach because we could print only a limited portion of the file using a for loop
# ! However, reading ALL LINES into a list of lists and then iterating over it is still not an efficient solution for large files, i.e., file with size in GBs. 
```

    104.229.180.74 - - [04/Mar/2018:21:32:18 +0000] "GET / HTTP/1.1" 404 571 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36" "-"
    104.229.180.74 - - [04/Mar/2018:21:32:18 +0000] "GET /favicon.ico HTTP/1.1" 404 571 "https://hilite.clarksonmsda.org/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36" "-"
    104.229.180.74 - - [04/Mar/2018:21:32:24 +0000] "GET / HTTP/1.1" 404 571 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36" "-"
    104.229.180.74 - - [05/Mar/2018:02:12:32 +0000] "GET / HTTP/1.1" 200 2518 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36" "-"
    
We are looking for solutions where we read & process only one line at a time while iterating through all rows of a file, so that minimum memory is utilized and hence, the memory would not be a bottleneck for reading big data files.
  ## (B) CSV module

CSV module provides two different classes to read the contents of a csv file:  csv.reader and csv.DictReader

With csv module’s reader class object we can iterate over the lines of a csv file as a list of values, where each value in the list is a cell value. 

### (i) Using the csv module's reader class object


```python
import csv
'''
For large CSV files, it is better to use the reader object in a for loop.
This avoids loading the entire file into memory at once.
'''
#counter to limit print content
n=0

#Importing reader function of csv module
from csv import reader

# open file in read mode
with open('access_subset.log', 'r') as f:
    
    # pass the file object to reader() to get the reader object
    rows = csv.reader(f)
    
    # Iterate over each of the rows in the csv using reader object
    for row in rows:
        # row variable is a list that represents a row in csv
        print(type(row))
        print(len(row))
        print(row)
        if n==3:
            break
        n+=1
'''
It iterates over all the rows of access_subset.log file.
For each row it fetched the contents of that row as a list and printed that list.

How did it work ?

It performed the following steps:-

Open the file in read mode and create a file object. [with open('access_subset.log', 'r') as f:]

Create a reader object (iterator) by passing file object in csv.reader() function. [rows = reader(f)]

Now, once we have an iterator (returned by the reader object) we used this iterator with for loop to read
individual row of the csv as list of values (where each value in the list represents an individual cell).

In this way, only one line will be in the memory at a time while iterating through csv file. 
'''


# Thus, we got a memory efficient solution :-)
```

    <class 'list'>
    2
    ['104.229.180.74 - - [04/Mar/2018:21:32:18 +0000] "GET / HTTP/1.1" 404 571 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML', ' like Gecko) Chrome/64.0.3282.140 Safari/537.36" "-"']
    <class 'list'>
    2
    ['104.229.180.74 - - [04/Mar/2018:21:32:18 +0000] "GET /favicon.ico HTTP/1.1" 404 571 "https://hilite.clarksonmsda.org/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML', ' like Gecko) Chrome/64.0.3282.140 Safari/537.36" "-"']
    <class 'list'>
    2
    ['104.229.180.74 - - [04/Mar/2018:21:32:24 +0000] "GET / HTTP/1.1" 404 571 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML', ' like Gecko) Chrome/64.0.3282.140 Safari/537.36" "-"']
    <class 'list'>
    2
    ['104.229.180.74 - - [05/Mar/2018:02:12:32 +0000] "GET / HTTP/1.1" 200 2518 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML', ' like Gecko) Chrome/64.0.3282.140 Safari/537.36" "-"']
    

### (ii) Using csv module's DictReader object
With csv module’s DictReader class object we can iterate over the lines of a csv file as a dictionary i.e.:
for each row a dictionary is returned, which contains the pair of column names and cell values for that row.


```python
n=0
from csv import DictReader

with open('wifi_list.csv', 'r') as f:
    
    # pass the file object to DictReader() to get the DictReader object
    rows = DictReader(f)
    
    # iterate over each line as a ordered dictionary
    for row in rows:
        
        # row variable is a dictionary that represents a row in csv
        print(row)
        if n==3:
            break
        n+=1
```

    OrderedDict([('MAC', 'e0:91:f5:de:fa:4f'), ('SSID', 'NETGEAR-5G'), ('AuthMode', '[WPS][ESS]'), ('FirstSeen', '5/9/2012 2:02'), ('Channel', '36'), ('RSSI', '-67'), ('CurrentLatitude', '44.67023722'), ('CurrentLongitude', '-74.98363701'), ('AltitudeMeters', '104.1999969'), ('AccuracyMeters', '7'), ('Type', 'WIFI')])
    OrderedDict([('MAC', 'e2:91:f5:de:fa:51'), ('SSID', 'bonesaw_Guest'), ('AuthMode', '[WPA-PSK-TKIP+CCMP][WPA2-PSK-TKIP+CCMP][ESS]'), ('FirstSeen', '5/9/2012 2:02'), ('Channel', '11'), ('RSSI', '-62'), ('CurrentLatitude', '44.67023722'), ('CurrentLongitude', '-74.98363701'), ('AltitudeMeters', '104.1999969'), ('AccuracyMeters', '7'), ('Type', 'WIFI')])
    OrderedDict([('MAC', 'e0:91:f5:de:fa:50'), ('SSID', 'BONESAW_HQ'), ('AuthMode', '[WPA-PSK-TKIP+CCMP][WPA2-PSK-TKIP+CCMP][WPS][ESS]'), ('FirstSeen', '5/9/2012 2:02'), ('Channel', '11'), ('RSSI', '-62'), ('CurrentLatitude', '44.67023722'), ('CurrentLongitude', '-74.98363701'), ('AltitudeMeters', '104.1999969'), ('AccuracyMeters', '7'), ('Type', 'WIFI')])
    OrderedDict([('MAC', 'e0:91:f5:5e:34:60'), ('SSID', 'Happy_Clam'), ('AuthMode', '[WPA-PSK-TKIP+CCMP][WPA2-PSK-TKIP+CCMP][WPS][ESS]'), ('FirstSeen', '5/9/2012 2:02'), ('Channel', '6'), ('RSSI', '-61'), ('CurrentLatitude', '44.67023722'), ('CurrentLongitude', '-74.98363701'), ('AltitudeMeters', '104.1999969'), ('AccuracyMeters', '7'), ('Type', 'WIFI')])
    

## (C) Some other useful things

### (i) Reading a csv file without header

#### (a) The book-ish method


```python
# skip first line i.e. read header first and then iterate over each row od csv as a list
n=0
with open('tobaccouse.csv', 'r') as f:
    rows = reader(f)
    header = next(rows)
    # Check file as empty
    if header != None:
        # Iterate over each row after the header in the csv
        for row in rows:
            # row variable is a list that represents a row in csv
            print(row)
            if n==2:
                break
            n+=1
    
'''
It would skip the header row of csv file and iterate over all the remaining rows of 'tobaccouse.csv' file. 
For each row it fetched the contents of that row as a list and printed that list. 
It initially saved the header row in a separate variable.

How did it work ?

As reader() function returns an iterator object, which we can use with Python for loop to iterate over the rows.
But in the above example we called the next() function on this iterator object initially,
which returned the first row of csv. 
After that we used the iterator object with for loop to iterate over remaining rows of the csv file.

'''

```

    ['2011', 'KS', 'Kansas', 'Tobacco Use - Survey Data', 'Cigarette Use (Youth)', 'Smoking Status', 'Ever Cigarette Smoker', 'YRBSS', 'Ever', '%', 'Percentage', '39.5', '', '', '2.4', '34.8', '44.4', '1080', 'Overall', 'All Ages', 'White', 'High School', '(38.3477403, -98.200781227)', 'BEH', '106BEH', '169SSA', '1GEN', '8AGE', '5RAC', '2EDU', 'YRB03', '3']
    ['1999', 'WY', 'Wyoming', 'Tobacco Use - Survey Data', 'Cigarette Use (Youth)', 'Smoking Status', 'Current Cigarette Smoker', 'YRBSS', 'Current', '%', 'Percentage', '34.6', '', '', '2.2', '30.3', '39.1', '747', 'Male', 'All Ages', 'All Races', 'High School', '(43.235541343, -108.109830353)', 'BEH', '106BEH', '169SSA', '2GEN', '8AGE', '6RAC', '2EDU', 'YRB01', '1']
    ['2009', 'DE', 'Delaware', 'Tobacco Use - Survey Data', 'Cigarette Use (Youth)', 'Smoking Status', 'Current Cigarette Smoker', 'YRBSS', 'Current', '%', 'Percentage', '8.3', '', '', '0.8', '6.8', '10.1', '1791', 'Female', 'All Ages', 'All Races', 'Middle School', '(39.008830667, -75.577741168)', 'BEH', '106BEH', '169SSA', '3GEN', '8AGE', '6RAC', '1EDU', 'YRB01', '1']
    




    "\nIt would skip the header row of csv file and iterate over all the remaining rows of 'tobaccouse.csv' file. \nFor each row it fetched the contents of that row as a list and printed that list. \nIt initially saved the header row in a separate variable.\n\nHow did it work ?\n\nAs reader() function returns an iterator object, which we can use with Python for loop to iterate over the rows.\nBut in the above example we called the next() function on this iterator object initially,\nwhich returned the first row of csv. \nAfter that we used the iterator object with for loop to iterate over remaining rows of the csv file.\n\n"



#### (b) Reading a csv file without header: A much simpler approach!


```python
n=0
with open('tobaccouse.csv','r') as f:
    rows = reader(f)
    for row in rows:
        if n>0:
            print(row)
            if n>3:
                break
        n+=1
```

    ['2011', 'KS', 'Kansas', 'Tobacco Use - Survey Data', 'Cigarette Use (Youth)', 'Smoking Status', 'Ever Cigarette Smoker', 'YRBSS', 'Ever', '%', 'Percentage', '39.5', '', '', '2.4', '34.8', '44.4', '1080', 'Overall', 'All Ages', 'White', 'High School', '(38.3477403, -98.200781227)', 'BEH', '106BEH', '169SSA', '1GEN', '8AGE', '5RAC', '2EDU', 'YRB03', '3']
    ['1999', 'WY', 'Wyoming', 'Tobacco Use - Survey Data', 'Cigarette Use (Youth)', 'Smoking Status', 'Current Cigarette Smoker', 'YRBSS', 'Current', '%', 'Percentage', '34.6', '', '', '2.2', '30.3', '39.1', '747', 'Male', 'All Ages', 'All Races', 'High School', '(43.235541343, -108.109830353)', 'BEH', '106BEH', '169SSA', '2GEN', '8AGE', '6RAC', '2EDU', 'YRB01', '1']
    ['2009', 'DE', 'Delaware', 'Tobacco Use - Survey Data', 'Cigarette Use (Youth)', 'Smoking Status', 'Current Cigarette Smoker', 'YRBSS', 'Current', '%', 'Percentage', '8.3', '', '', '0.8', '6.8', '10.1', '1791', 'Female', 'All Ages', 'All Races', 'Middle School', '(39.008830667, -75.577741168)', 'BEH', '106BEH', '169SSA', '3GEN', '8AGE', '6RAC', '1EDU', 'YRB01', '1']
    ['1995', 'WY', 'Wyoming', 'Tobacco Use - Survey Data', 'Cigarette Use (Youth)', 'Smoking Status', 'Frequent Cigarette Smoker', 'YRBSS', 'Frequent', '%', 'Percentage', '19.6', '', '', '1.5', '16.7', '22.8', '798', 'Female', 'All Ages', 'All Races', 'High School', '(43.235541343, -108.109830353)', 'BEH', '106BEH', '169SSA', '3GEN', '8AGE', '6RAC', '2EDU', 'YRB02', '2']
    

### (ii) A (very) handy method to get a list of dictionary out of the serialized data


```python
#Use the following code to get a list of dictionary

import csv
with open('wifi_list.csv') as f:
    data = [{k: str(v) for k, v in row.items()}
    for row in csv.DictReader(f, skipinitialspace=True)]
    
    print(data[1])
```

    {'MAC': 'e2:91:f5:de:fa:51', 'SSID': 'bonesaw_Guest', 'AuthMode': '[WPA-PSK-TKIP+CCMP][WPA2-PSK-TKIP+CCMP][ESS]', 'FirstSeen': '5/9/2012 2:02', 'Channel': '11', 'RSSI': '-62', 'CurrentLatitude': '44.67023722', 'CurrentLongitude': '-74.98363701', 'AltitudeMeters': '104.1999969', 'AccuracyMeters': '7', 'Type': 'WIFI'}
    

### (iii) Implementing an efficient search technique (Like the  'WHERE' clause of SQL) 


```python
# Open wifi.csv iterate through file. 
# Print the line number WHERE the row contains ‘CPH’
with open ('wifi_list.csv', 'r') as f:
    i=0
    for line in f:
        if 'CPH' in line:
            print(i)
            break
        i+=1
```

    185
    


```python
# Open wifi.csv iterate through file.
# Print the total number of lines which contains ‘CPH’
n=0
with open ('wifi_list.csv', 'r') as f:
    i=0
    for line in f:
        if 'CPH' in line:
            n+=1
            
        i+=1
print(n)
```

    97
    


```python
# Use the CSV parser to read each line at a time as a list.
# Count the number of SSIDs which contain CPH.
import csv
n=0
with open ('wifi_list.csv', 'r') as f:
    reader = csv.reader(f)
    for  line in reader:
        if 'CPH' in line[1]:
            n +=1                   
print(n)
```

    97
    
