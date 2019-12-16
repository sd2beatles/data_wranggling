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

One of the prominent features of R programming languages is to show a statistical summary of a whole input data. Unfortunately, Python does not provide any function allowing us to glimpse our data.  If it does, the function is not as much as we expect it would be. 

Let's write a code to review a summary of our data. 

```python

def data_summary(data,shape=True,skew_threshold=2,kurtosis_threshold=3):
    #store data type of each input
    data_types=data.dtypes
    #the number of non-missing data 
    data_count=data.count()
    #missing rate
    data_missing=round(data.isnull().sum()/data.shape[0]*100,2)
    #sknewness data
    data_skew=data.skew()
    #kurtosis
    data_kurtosis=data.kurtosis()
    
    #create a object to store all the information above and make it datafrmae format
    table=pd.concat([data_types,data_count,data_missing,data_skew,data_kurtosis],axis=1)
    table.columns=['data_types','data_count',"missing_rate",'skewness','kurtosis']
    
    if shape==True:
        sknewss=table.skewness.dropna().map(lambda x: True if np.abs(x)>=skew_threshold else False )
        result_skew=table.skewness.dropna()[sknewss]
        kurtosis=table.loc[:,'kurtosis'].dropna().map(lambda x: True if x>=kurtosis_threshold else False )
        result_kurtosis=table.skewness.dropna()[kurtosis]
    
    print("Data Shape:{0}".format(data.shape))
    print('-'*100)
    print("Data Types and thier Counts:\n{0}".format(table.data_types.value_counts()))
    print('-'*100)
    print("Skewness Of Quantitative Data:\n{0}".format(result_skew))
    print('-'*100)
    print("Kurtosis of Quantitative Data:\n{0}".format(result_kurtosis))
    print('-'*100)
    missing_str=data_missing.astype('str')
    print("The missing Rate: {0}".format(missing_str.map(lambda x: x+'%')))
```
![image](https://user-images.githubusercontent.com/53164959/70679348-28b9cb80-1cd8-11ea-88a1-af1a46740f94.png)

### 4. Missing Values 
![image](https://user-images.githubusercontent.com/53164959/70906480-d36c1a00-2049-11ea-8858-23fd0ddcf090.png)


Step 1) The following code returns columns whose missing rate is higher than 50 %, which deems to provide necessary information for our upcoming data analysis. Therefore, we should get them left from our input features, which contributes to the reduction of the data dimension 

```python
#store column index whose missing value>50
removed_index=[]
missing_rate=data.isnull().sum()/data.shape[0]*100
for i,j in zip(missing_rate.index,missing_rate):
    if j>50:
        removed_index.append(i)
#remove the columns with missing rate>50 from our input features
data=data.drop(removed_index,axis=1)

#In case where we have missing values 
data.replace('None',np.nan,inplace=True)

```
step 2) Since missing inputs are more than one, we consider any tools that impute the data multiply. From the library with the name of  fancyimpute, we can import MICE to make this happen. Just like machine learning libraries, the MICE library requires an extra step to get our input features dummied before implemented. 

```python
category_index=[]
for i in range(data.shape[1]):
    if data.iloc[:,i].dtype=='object':
        data.iloc[:,i]=data.iloc[:,i].astype('category')
        category_index.append(i)
cat_vars=data.columns[category_index]

for var in cat_vars:
    cat_list=pd.get_dummies(data[var],prefix=var)
    data=data.join(cat_list)

data_vars=data1.columns.values.tolist()
final_columns=[i for i in data_vars if i not in category_index]
data=data[final_columns]    
```

step 3) Implement multiple_impuation 




