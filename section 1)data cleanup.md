### 1. Identifying Values for Data Cleanup

To translate ambiguous or abbreviated header to more human-readable version, you definately obtain an easy-to-use abbrevaition list. 

```python
from csv import reader
data_dr=reader(open('...\mn.csv','r'))
#Python 3 opens text fiels in the locale default encoding; if that encoding cannot handle the unicoe values you are trying to write,pick
#a different code. 
header_dr=reader(open('\mn_headers_updated.csv','r',encoding='UTF8'))
```

Make sure that we are trained to compare the number of input features in our data sets. 
Here,data_rows have more than what the header_rows have.Therefore,any column not to be assigned any readable name would be called the current notation.

```python
data_rows=[i for i in data_dr]
#note that header_rows containing abbreviated_names,full_names,question
header_rows=[h for h in header_dr if h[0] in data_rows[0]]
print("Number of Input Features:{0}".format(len(data_rows[0])))
print("Number of header Descript:{0}".format(len(header_rows)))

#Note header rows contain three information: abbreviated name, full name, question
#Createa a dictionary file only containg the first two information


#store only abbreviated names matched to header_rows 
all_short_headers=[h[0] for h in header_rows]

#index that does not have a proper name 
skip_index=[]
#header we are interested in (this contains all pertaining information)
final_header_rows=[]

for header in data_rows[0]:
    if header not in all_short_headers:
        index=data_rows[0].index(header)
        skip_index.append(index)
    else:
        for head in header_rows:
            if head[0]==header:
                #Note that we include all the pertinent information here(ie abbreviated_names,full_names,question)
                final_header_rows.append(head)
                break
new_data=[]

for row in data_rows[1:]:
    new_row=[]
    #Since the list does only allow for slicing and integer when using .remove(), we should undergo semmingly burdensome work. 
    for i,d in enumerate(row):
        if i not in skip_index:
            new_row.append(d)
    new_data.append(new_row)

zipped_data=[]
#Zips each row(now exactly matched with header with data) and adds it to a new array,zipped_data
for drow in new_data:
    zipped_data.append(zip(final_header_rows,drow))   
```
### 2. Formatting Data

One of the most common forms of data cleanup is getting your unreadable or hardto-
read data and data types to fit a proper readable format

A more advanced way to turn objects into strings or Python representations is to utilize
the format method. As clarified in the Python documentation, this method lets
us define a string and pass the data as arguments or keyword arguments into the
string. Let’s take a closer look at format

```python
#Store the first row of zippe_data
a=list(zipped_data[0])

for x in a:
    print("Question:{0}\nAnswer:{1}".format(x[0],x[1]))
```
![image](https://user-images.githubusercontent.com/53164959/70108966-eec04800-168d-11ea-8b49-7c7bab073424.png)

This is fairly difficult to read. Let’s try cleaning it up a bit more. From our output, we
can see the question tuple has an abbreviation as the 0-index value and a description
of the question as the 1-index value. We’d like to just use *_the second part of the array_*,
which gives us a nice title. Let’s try again:

```python
#Revised format
for x in a:
    print("Question:{[1]}\nAnswer:{}".format(x[0],x[1]))
```
![image](https://user-images.githubusercontent.com/53164959/70109034-11526100-168e-11ea-9f10-c010609cec09.png)
