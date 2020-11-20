
# Writing new files

## Writer object
A writer object lets you write data to a CSV file. 
To create a writer object, you use the csv.writer() function.


```python
import csv

Output_CSV = open('output_example1.csv', 'w', newline='')
Writer = csv.writer(Output_CSV)
Writer.writerow(['value1', 'value2', 'value3', 'value4'])
Writer.writerow(['Value, 5', 'value6', 'value7', 'value8'])
Output_CSV.close()

with open('output_example1.csv', 'r') as f:
    reader = csv.reader(f)
    for line in reader:
        print(line)
'''
The csv module handles the extra commas that are (confusingly) put inside the values.
The module does not create 2 seperate values if it finds a comma INSIDE a value. 
See value 5 for example  .. this is a huge advantage!

Don't forget to set the newline parameter as nothing! ... it means put nothing inside those quotation marks '',
Because if you don't do that, the output CSV will contain a blanc row inbetween each of the rows containing data!
Well, that is true for the windows OS, I didn't have this problem in others.
'''

```

    ['value1', 'value2', 'value3', 'value4']
    ['Value, 5', 'value6', 'value7', 'value8']
    




    "\nThe csv module handles the extra commas put inside the values.\nIt does not create 2 seperate values if it finds a comma INSIDE a value. \nSee value 5 for example  .. this is a huge advantage!\n\nDon't forget to set the newline parameter as nothing! ... it means put nothing inside those quotation marks '',\nBecause if you don't do that, the output CSV will contain a blanc row inbetween each of the rows containing data!\nWell, that is true for the windows OS, I didn't have this problem in others.\n"



## DictReader and DictWriter CSV Objects

From "Automate the Boring Stuff with Python, 2nd Edition" (https://www.google.com/books/edition/Automate_the_Boring_Stuff_with_Python_2n/TVz6DwAAQBAJ?hl=en&gbpv=0)


For CSV files that contain header rows, it’s often more convenient to work with the DictReader and DictWriter objects,
rather than the reader and writer objects.

The reader and writer objects read and write to CSV file rows by using lists. 
The DictReader and DictWriter CSV objects perform the same functions but use dictionaries instead, 
and they use the first row of the CSV file as the keys of these dictionaries.


### DictReader Objects
Inside the loop, DictReader object sets row to a dictionary object with keys derived from the headers in the first row. (Well, technically, it sets row to an OrderedDict object, which you can use in the same way as a dictionary; the difference between them is beyond the scope of this book.) Using a DictReader object means you don’t need additional code to skip the first row’s header information, since the DictReader object does this for you.

DictWriter objects use dictionaries to create CSV files



```python
import csv
Output_CSV2 = open('output_example2.csv', 'w', newline='')
DictWriter = csv.DictWriter(Output_CSV2, ['Fruit', 'Taste', 'Diabetic_Code'])
DictWriter.writeheader()
DictWriter.writerow({'Fruit': 'Cucumber', 'Taste': 'Weird', 'Diabetic_Code': '0_Can eat unlimited'})

DictWriter.writerow({'Fruit': 'Grape', 'Taste': 'Sweet', 'Diabetic_Code': '2_Can not eat'})
DictWriter.writerow({'Taste': 'Sour', 'Fruit': 'Strawberry'})

Output_CSV2.close()
lines =[]
with open('output_example2.csv', 'r') as f:
    reader = csv.DictReader(f)
    for line in reader:
        lines.append(line)
f.close()
print(lines[0:3])

```

    [OrderedDict([('Fruit', 'Cucumber'), ('Taste', 'Weird'), ('Diabetic_Code', '0_Can eat unlimited')]), OrderedDict([('Fruit', 'Grape'), ('Taste', 'Sweet'), ('Diabetic_Code', '2_Can not eat')]), OrderedDict([('Fruit', 'Strawberry'), ('Taste', 'Sour'), ('Diabetic_Code', '')])]
    

## Creating a new CSV taking subset of a larger file

Here is a handy code chunk that will help you to create a new CSV file with only one out of every hundred rows of a large CSV

### Writing the new file


```python
# The codes below are to create a new csv  which is a subset (having only 1 row for every 100 rows) of the original file
# Name of the new csv: 'subset_pvsubset.csv' 
# Name of the original csv: 'pvsubset.csv'
# Both CSV files are given in this repository
import csv
f1 = open('subset_pvsubset.csv',"w", newline ='') # A new CSV is being created! Hence the "w" mode.
f1.write("") # This will clear the content of the file. Handy for repeated  testing the creation of an output file with same code!
f1.close()
line = 0 # Setting a counter so that we can use this to write specific lines in the output file
f1 = open('subset_pvsubset.csv',"a")
writer = csv.writer(f1, delimiter = ',', lineterminator = '\n')

with open('pvsubset.csv', 'r') as f2:
    reader = csv.reader(f2)
    for row in reader:
        if line == 0: # A simele way to write the header in the output file!
            writer.writerow(row)
        elif line % 100 == 0: # This will ensure writing 1 for each 100 rows from the source file.
            writer.writerow(row)
        line+=1            
f1.close()
```

### Checking the newly written CSV


```python
# checking content of new csv
n =0
with open('subset_pvsubset.csv', 'r') as nf:
    reader = csv.reader(nf)
    for row in reader:
        print(row, '\n')
        if n >1:
            break
        n+=1
```

    ['Summons Number', 'Plate ID', 'Registration State', 'Issue Date', 'Violation Code', 'Vehicle Body Type', 'Vehicle Make', 'Vehicle Expiration Date', 'House Number', 'Street Name', 'Vehicle Color', 'Vehicle Year'] 
    
    ['1363748579', 'RR76Y0', 'PA', '7/9/2015', '20', 'SUBN', 'VOLKS', '0', '543', 'ATLANTIC AVE', 'GY', '0'] 
    
    ['1320811292', 'GXD9050', 'NY', '6/21/2017', '40', 'SUBN', 'NISSA', '20170303', '1050', 'SOUNDVIEW AVE', 'GRAY', '2005'] 
    
    

### Comparing file sizes (original VS new)


```python
import os
print("Original file size : "+str(os.stat('pvsubset.csv').st_size))
print("New file size : "+str(os.stat('subset_pvsubset.csv').st_size))
```

    Original file size : 8641694
    New file size : 86412
    
