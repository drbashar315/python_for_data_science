
 # Handling MySQL database with efficient time management
  
PyMySQL is an interface for connecting to a MySQL database server from Python. It implements the Python Database API v2.0 and contains a pure-Python MySQL client library.
Read more about this at https://www.tutorialspoint.com/python3/python_database_access.htm

## 1. Establishing  Connnection and Cursor


```python
import pymysql
conn = pymysql.connect(host='http://www.host.name OR localhost', \
                       port=3306, \
                       user='username', \
                       passwd='p@ssword', \
                       db='database_name', autocommit=True)
cur = conn.cursor(pymysql.cursors.DictCursor)
#cur = conn.cursor() # if DictCursor is not required 
# about cursor: read more here: (https://eric.lubow.org/2010/when-to-use-mysql-cursor-classes-in-python/)
```

## 2. Some example commands

### A. CREATE TABLE


```python
# Example code for creating a table:

cur.execute('''

CREATE TABLE IF NOT EXISTS `pvsubset` (
  `ticket_id` int(5) NOT NULL AUTO_INCREMENT,
  `summons_number` varchar(10) NOT NULL,
  `plate_id` varchar(10) NOT NULL,
  `registration_state` varchar(2) NOT NULL,
  `issue_date` date NOT NULL,
  `violation_code` varchar(2) NOT NULL,
  `vehicle_body_type` varchar(10) NOT NULL,
  `vehicle_make` varchar(10) NOT NULL,
  `vehicle_expiration_date` date NOT NULL,
  `house_number` varchar(20) NOT NULL,
  `street_name` varchar(100) NOT NULL,
  `vehicle_color` varchar(20) NOT NULL,
  `vehicle_year` varchar(4) NOT NULL,
   PRIMARY KEY (`ticket_id`)
) ENGINE=MyISAM DEFAULT CHARSET=latin1 ;
''')
```

### B. SELECT FROM table


```python
# Example of fetching the result of a SELECT command
# for loop is used here to limit the print output size

cur.execute("SELECT * FROM CoreFact ")

myresult = cur.fetchall()
for x in myresult:
    print(x)
    #break
```

## 3. Example of an INSERT script: Better efficiency through "Query grouping"

Here is an example of query grouping (sending queries in a specified block size) for inserting large number of rows into a database table.

The timimg is monitored using time.time() functions within the script.


```python
import pymysql,csv,time
sql = '''
INSERT INTO `pvsubset2`
(
`summons_number`,`plate_id`, `registration_state`,`issue_date`,`violation_code`,
`vehicle_body_type`,`vehicle_make`,`vehicle_expiration_date`,`house_number`,`street_name`,`vehicle_color`, 
`vehicle_year`
)
 VALUES (%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s);
 ''' 

tokens =[]
n= 0 
i=0
blocksizes = [2500] # The size is taken somewhat arbitrarily. Check the next section about optimizing this.



with open('pvsubset.csv') as f:
    data = [{k: str(v) for k, v in row.items()}
        for row in csv.DictReader(f, skipinitialspace=True)]
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

## 4. Date fields modification

In the previous INSERT script, some data are truncted by MySQL (for example: "1265, "Data truncated for column 'vehicle_expiration_date' ") due to errors in some of the date fields of the source (pvsubset.csv).

To get rid of those errors, the date fields are manipulated using the datetime module of python.


```python
import datetime
sql = '''
INSERT INTO `pvsubset`
(
`summons_number`,`plate_id`, `registration_state`,`issue_date`,`violation_code`,
`vehicle_body_type`,`vehicle_make`,`vehicle_expiration_date`,`house_number`,`street_name`,`vehicle_color`, 
`vehicle_year`
)
 VALUES (%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s);
 ''' 

tokens =[]
n= 0 
i=0
blocksizes = [4700]


with open('pvsubset.csv') as f:
    data = [{k: str(v) for k, v in row.items()}
        for row in csv.DictReader(f, skipinitialspace=True)]

for bs in blocksizes:
    start = time.time()


    for line in data:
        datetime_str1 = line["Issue Date"]
        datetime_obj1 = datetime.datetime.strptime(datetime_str1, '%m/%d/%Y')
        new_datetime_str1 = datetime_obj1.strftime("%Y-%m-%d") # This is to maintain consistancy in the datetime format

        try:
            datetime_str2 = line["Vehicle Expiration Date"]
            datetime_obj2 = datetime.datetime.strptime(datetime_str2, '%Y%m%d')
            new_datetime_str2 = datetime_obj2.strftime("%Y-%m-%d") # This is to maintain consistancy in the datetime format
        except:
            new_datetime_str2 = '0000-00-00' # Because many of the date fields are empty or wrong in the source file


        tokens.append((line["Summons Number"],
                  line["Plate ID"],
                  line["Registration State"],
                  new_datetime_str1,
                  line["Violation Code"],
                  line["Vehicle Body Type"],
                  line["Vehicle Make"],
                  new_datetime_str2,
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

    block size: 4700 - total time : 16.09080457687378
    

## 5. Even better time efficiency: Finding the optimum block size

In the following modification of the previous code different block sizes (ranginging between 500 and 5000) were tested and timed. Thus, instead of picking an arbitrary block size, we can find the optimum one.


```python
sql = '''
INSERT INTO `pvsubset`
(
`summons_number`,`plate_id`, `registration_state`,`issue_date`,`violation_code`,
`vehicle_body_type`,`vehicle_make`,`vehicle_expiration_date`,`house_number`,`street_name`,`vehicle_color`, 
`vehicle_year`
)
 VALUES (%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s);
 ''' 

tokens =[]
n= 0 
i=0
blocksizes = [50, 100, 200,500,1000,1500,2000,2500,3000,3500,4000,4500,4600, 4700, 5000,5500, 5600, 5700,6000,6500]


with open('pvsubset.csv') as f:
    data = [{k: str(v) for k, v in row.items()}
        for row in csv.DictReader(f, skipinitialspace=True)]

for bs in blocksizes:
    start = time.time()


    for line in data:
        datetime_str1 = line["Issue Date"] 
        datetime_obj1 = datetime.datetime.strptime(datetime_str1, '%m/%d/%Y')
        new_datetime_str1 = datetime_obj1.strftime("%Y-%m-%d") 

        try:
            datetime_str2 = line["Vehicle Expiration Date"]
            datetime_obj2 = datetime.datetime.strptime(datetime_str2, '%Y%m%d')
            new_datetime_str2 = datetime_obj2.strftime("%Y-%m-%d")  
        except:
            new_datetime_str2 = '0000-00-00' 


        tokens.append((line["Summons Number"],
                  line["Plate ID"],
                  line["Registration State"],
                  new_datetime_str1,
                  line["Violation Code"],
                  line["Vehicle Body Type"],
                  line["Vehicle Make"],
                  new_datetime_str2,
                  line["House Number"],
                  line["Street Name"],
                  line["Vehicle Color"],
                  line["Vehicle Year"]))
        if i % bs == 0 and i != 0:
            n+=1
            #print(tokens)
            bstart = time.time()
            cur.executemany(sql,tokens)
            conn.commit()
            #print "Block " + str(n) +" "+ str(time.time() - bstart)
            tokens = []
        i+=1
    print ("block size: " + str(bs) + " - total time : " + str(time.time() - start))
    if len(tokens) > 0:
        cur.executemany(sql,tokens)
        conn.commit()
    
cur.close()
conn.close()
```

    block size: 50 - total time : 104.20536756515503
    block size: 100 - total time : 62.32541227340698
    block size: 200 - total time : 38.04236841201782
    block size: 500 - total time : 27.973157167434692
    block size: 1000 - total time : 23.740208387374878
    block size: 1500 - total time : 21.964792251586914
    block size: 2000 - total time : 21.535418272018433
    block size: 2500 - total time : 21.10867166519165
    block size: 3000 - total time : 22.265390157699585
    block size: 3500 - total time : 23.39005947113037
    block size: 4000 - total time : 18.887829780578613
    block size: 4500 - total time : 16.934757232666016
    block size: 4600 - total time : 16.862687826156616
    block size: 4700 - total time : 16.431373596191406
    block size: 5000 - total time : 17.56948709487915
    block size: 5500 - total time : 16.624005556106567
    block size: 5600 - total time : 16.666142225265503
    block size: 5700 - total time : 15.950990676879883
    block size: 6000 - total time : 16.418246269226074
    block size: 6500 - total time : 16.66899800300598
    


```python

```
