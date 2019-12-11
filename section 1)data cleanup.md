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

2.1 Qualitative Data Handling

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
    if x[0][2] !='':
        print("Question:{0}\nAnswer:{1}".format(x[0][2],x[1]))
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

2.2 Numerical Data Handling

Our current dataset doesn’t have a large amount of numeric data, so we’ll just use some example numbers to show more formatting
options for different numerical types

```python
# Data Preparation
clients={}
David={'Height':176,
        'Weight':54,
        'vision':-10.2}

Han={'Height':235,
        'Weight':23,
        'vision':-11.2}
clients.update({'David':David})
clients.update({'Han':Han})

# Setting up the format in which you want to present your data
string_to_print='Height:{Height:.4f}\n'
string_to_print+='Weight:{Weight:}\n'
string_to_print+='vision:{vision:.2f}'

print(string_to_print.format(**clients['David']))

```
What is unpacking a dictionary?

_Unpacking a Python dictionary will send the key/value pairs in expanded form; here, the unpacked keys and values are sent to
the format method._


2.3 Date Format Data Handling

Imagine you have selected one of the rows from zipped_data and wonder if you can add the time taken for the interview. 
This data is added to the list at the end of the index called duration and dur for short. Here is the step by step procedures to implement what we are wishing to !

```python
#Select one of the rows from zipped_data
a=list(zipped_data[0])

#Prepare  a list to locate where our interested information is
for x in a:
    print("Question:{0}\nAnswer:{1}".format(x[0],x[1]))
```

We have found the following information

```
index number:7, Contents:['MWM6D', 'Day of interview']
index number:8, Contents:['MWM6M', 'Month of interview']
index number:9, Contents:['MWM6Y', 'Year of interview']

index number:13, Contents:['MWM10H', 'Start of interview - Hour']
index number:14, Contents:['MWM10M', 'Start of interview - Minutes']
index number:15, Contents:['MWM11H', 'End of interview - Hour']
index number:16, Contents:['MWM11M', 'End of interview - Minutes']
```

```python
from datetime import datetime
#create a date string and pattern string using the syntax defined in the python 
record_format='%m/%d/%Y %H:%M'

#create a base string to parse all the relevant data from the many entries 
#note that a=list(zipped_data[0])
start_string='{}/{}/{} {}:{}'.format(a[8][1],a[7][1],a[9][1],a[13][1],a[14][1])
end_string='{}/{}/{} {}:{}'.format(a[8][1],a[7][1],a[9][1],a[15][1],a[16][1])
start_time=datetime.strptime(start_string,record_format)
end_time=datetime.strptime(end_string,record_format)

#calculate the time diff
duration=end_time-start_time

#add to our data
a.append((['dur','duration',''],duration.total_seconds()/60))

##print the last section of data
print(a[-1])
