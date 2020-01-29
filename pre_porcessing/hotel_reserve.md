## 1. Data Prepartion

```sql

DROP  TABLE IF EXISTS hotel_tb CASCADE;
DROP TABLE IF EXISTS customer_tb CASCADE;
DROP TABLE IF EXISTS reserve_tb CASCADE ;


CREATE TABLE hotel_tb(
  hotel_id VARCHAR NOT NULL,
  base_price INT  NOT NULL,
  big_area_name  VARCHAR,
  small_area_name VARCHAR,
  hotel_latitude REAL,
  hotel_logitude REAL,
  is_business boolean,
  PRIMARY KEY(hotel_id));

CREATE TABLE customer_tb (
 customer_id VARCHAR,
 age INT,
 sex VARCHAR,
 home_latitude REAL,
 home_longitude REAL,
 PRIMARY KEY(customer_id));

CREATE TABLE reserve_tb(
	reserve_id VARCHAR NOT NULL,
	hotel_id VARCHAR NOT NULL,
	customer_id VARCHAR NOT NULL,
	reserve_datetime TIMESTAMP NOT NULL,
	checkin_date DATE NOT NULL,
  checkint_time TIME NOT NULL,
	checkout_date DATE NOT NULL,
	pople_num INT NOT NULL,
	total_price INT NOT NULL,
	PRIMARY KEY(reserve_id),
	FOREIGN KEY(hotel_id) REFERENCES hotel_tb(hotel_id),
	FOREIGN KEY(customer_id) REFERENCES customer_tb(customer_id)
	)
```

Things to Remember 
```
Primary keys is a colum that uniquely idenitfies every record in a certain table.
The rules of primary keys are 
- unique
- never chanigng(the value must stay unchanged and cnosistent)
- nver null 

Foreign keys is a column or set of columns in one table that uniquely identifies rows
in another table( This is a primary key in one table but classified as foreign key in another)

The properiteis of foreing key are
- no neccessary to be unique
- multiple foregin keys in one entity
```
## 2.Random Sample 

### 2.1 Simple Random Sample
For the case when we have to deal with a big zie of data, it is efficient to take a random sample from it. We can implement the 'RANDOM()' query.  
There is a thing to remember; sql does not usually support limit in subquery. You should type integer nummber at your disposal in order to limit the number of data rows.

```sql

SELECT *
FROM reserve_tb
ORDER BY RANDOM()
--limit our data rows to 12000*0.5
LIMIT CEIL(12000*0.5);
```

### 2.2  Samping by an Assigned ID

![image](https://user-images.githubusercontent.com/53164959/73349573-c724df00-42ce-11ea-8c45-de520709bc66.png)


In our sample data, if decide to take a specific amount of sample data based on the reserve_id, we are trapped into biased selection. 
That is, some of the customers do not have a chance to be included in the sample selection. Judging the basic attributes of our sample data,
we take a radom sample based ont customer_id. 

The steps are following

1)  Generate a random number and assign it to each customer_id
2)  Use FIRST_VALUE query to find the first value in ordered list grouped by customer_id 
    and set the column as rnd. 
3)  Select rows based on our rules rnd <=0.5



```sql
WITH reserve_tb_random AS(
	SELECT *,
	       --assign random number to each customer_id
           RANDOM() AS rnd 
	       FROM reserve_tb)
    ,selection_process AS(
	SELECT *,
       --FIRST_VALUE is always with window functio  OVER()
       FIRST_VALUE(rnd) OVER(PARTITION BY customer_id) AS random_number
       FROM reserve_tb_random
	)
	SELECT * 
	     FROM selection_process
		 WHERE random_number<=0.5;
```

## 3. Data Summary Skills

### 3.1Aggregation and Unique Counts

If you want to find out totla number of rservations and customers by hotel_id, the codes are following below

```sql
SELECT hotel_id,
     COUNT(reserve_id) AS res_cut,
     --note that DISTINCT returns the unique customer_id
	   COUNT(DISTINCT customer_id) AS cus_cut
	   FROM reserve_tb
	   GROUP BY hotel_id
	   ORDER BY hotel_id;
```

### 3.2 Calculating Sum

'Sum' Function is an aggregate function that calculates the sum of all or distinct values in an expression. 
Let's compute the total sum of price according to hotel_id and people. 

```sql
WITH sum_up AS(
  SELECT hotel_id,
	     people_num,
	     SUM(total_price)
	     FROM reserve_tb
	     GROUP BY hotel_id,people_num
	     ORDER BY people_num
)
SELECT * FROM sum_up;
```

### 3.2  MIN,MAX,





