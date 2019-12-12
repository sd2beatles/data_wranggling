### 1. Identifying Values for Data Cleanup

To translate ambiguous or abbreviated header to more human-readable version, you definately obtain an easy-to-use abbrevaition list. 

```python
data=pd.read_csv(r'C:\Users\DAVID SEO\Desktop\data_practice\mn\mn.csv')
data_header=pd.read_csv(r'C:\Users\DAVID SEO\Desktop\data_practice\mn\mn_headers_updated.csv')
```

### 2. Removing Irrevalent Input Features
An warming message appeared right after assinging values to data and data_header. There is some mixed types across some columns of data.
It may be definetely helpful to examine what cause the message to arise. 

```python
ct_names=data.columns[[110,111,112,120]]
for i in ct_names:
    display(data_header[data_header.Name==i])
    display("unique Values of {0} : {1}".format(i,data.loc[:,i].unique()))
    print("-"*100)
 ```
![image](https://user-images.githubusercontent.com/53164959/70676036-3b2f0780-1cce-11ea-9227-a4736c158baf.png)


The test result is a little astoudning in that each column does not  show the types of tabacco with the total number of missing data 
exceeding over 9000. We have an enough ground to treat them as irrelevant to our data analysis and remove them altogether. Furthermore, 
we have a column called 'Unnmaed 0" indicating the row number to be eminated as well. 

```python
ct_names=data.columns[[0,110,111,112,120]]
#eliminating the irrelevant columns
data=data.drop(ct_names,axis=1)
```

### 3. Data Summary



  
