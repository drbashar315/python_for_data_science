
# Handling Large Data sets

## 1. What is 'LARGE'? : Some rough estimates

### 0 – 1 MB
Can visually inspect in Excel.
Easy to visualize in Excel. 

### 1 – 50 MB
Can Read and process in Excel on a single computer.
50 MB – 1 GB
Can easily load and process in MySQL.
Excel starts having issues loading into memory.
Can process in Python using Pandas.

### 1 GB – 100 GB
MySQL optimizations / keys start to become a necessity.
Excel does not work for most operations.
Benefits of on-disk processing become apparent as the dataset exceeds single machine memory.

### 100 GB – 10T B 
Can still be processed on one machine with limitations.
Distributed solutions start to become beneficial.

### 10 TB - 1 PB
Single hard disk drives are not large enough to store entire dataset.
Distributed big data solutions start to become necessary.

### 1 PB +
Enterprise distributed clusters are needed.
You will need a full time team to manage data.
You will most likely develop custom technologies around your dataset.

## 2. Using Bash / Shell Scripts
# We can do some very basic operations with the built in tools of Unix /Linux 
# This would allow us to read directly from disk. Examples:-


#1.  Show last 3 lines of wifi_list.csv using:       tail -3 wifi_list.csv
#2.  Count the total rows in wifi_list.csv using:    cat wifi_list.csv | wc -l
#3.  Show first 3 lines of wifi_list.csv using:      head -3 wifi_list.csv

Bash command and output example:
-----------
$ head -3 wifi_list.csv
MAC,SSID,AuthMode,FirstSeen,Channel,RSSI,CurrentLatitude,CurrentLongitude,AltitudeMeters,AccuracyMeters,Type
e0:91:f5:de:fa:4f,NETGEAR-5G,[WPS][ESS],5/9/2012 2:02,36,-67,44.67023722,-74.98363701,104.1999969,7,WIFI
e2:91:f5:de:fa:51,bonesaw_Guest,[WPA-PSK-TKIP+CCMP][WPA2-PSK-TKIP+CCMP][ESS],5/9/2012 2:02,11,-62,44.67023722,-74.98363701,104.1999969,7,WIFI
----------
## 3. Python Read Line By Line Examples


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
    for i, line in enumerate(reader):
        if 'CPH' in line[1]:
            n +=1                   
print(n)
```

    97
    

## 4. Sending voluminous data to a database

### See the previous notebook: 2_Handling_Databases_And_Optimizing_Queries_PyMySQL.ipynb


```python
# Establishing the connection to a database by PyMySQL


import pymysql
conn = pymysql.connect(host='http://www.host.name OR localhost', \
                       port=3306, \
                       user='username', \
                       passwd='p@ssword', \
                       db='database_name', autocommit=True)
cur = conn.cursor(pymysql.cursors.DictCursor)

#cur = conn.cursor() # if DictCursor is not required 
# about cursor: read more here: (https://eric.lubow.org/2010/when-to-use-mysql-cursor-classes-in-python/)


sql = '''
INSERT INTO `pvsubset2`
(
`summons_number`,`plate_id`, `registration_state`,`issue_date`,`violation_code`,
`vehicle_body_type`,`vehicle_make`,`vehicle_expiration_date`,`house_number`,`street_name`,`vehicle_color`, 
`vehicle_year`)
 VALUES (%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s);
 ''' 
with open('pvsubset.csv') as f:
    data = [{k: str(v) for k, v in row.items()}
        for row in csv.DictReader(f, skipinitialspace=True)]
tokens =[]
n= 0 
i=0
blocksizes = [4700]

for bs in blocksizes:
    start = time.time()


    for line in data:
        tokens.append((line["Summons Number"],
                  line["Plate ID"],
                  line["Registration State"],
                  line["Issue Date"],
                  line["Violation Code"],
                  line["Vehicle Body Type"],
                  line["Vehicle Make"],
                  line["Vehicle Expiration Date"],
                  line["House Number"],
                  line["Street Name"],
                  line["Vehicle Color"],
                  line["Vehicle Year"]))
        if i % bs == 0:
            n+=1
            bstart = time.time()
            cur.executemany(sql,tokens)
            conn.commit()
            tokens = []
        i+=1
    print ("block size: " + str(bs) + " - total time : " + str(time.time() - start))
    if len(tokens) > 0:
        cur.executemany(sql,tokens)
        conn.commit()
    
cur.close()
```

    block size: 4700 - total time : 12.765304803848267
    
