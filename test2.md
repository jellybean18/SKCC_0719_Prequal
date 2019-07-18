# Part2 - Big Data Knowledge Test

## Problem 1
### Write a query to compare each active account’s balance to the average balance of all active accounts of the same type.
### The account data exists in the metastore account table in the problem1 database. The amount column gives the account balance. The type column gives the type of account and the status column gives the status of the account.
#### Write the report query in the local file: /home/training/problem1/solution.sql
#### Executing the solution.sql script from the hive command-line tools should generate the report output
#### Only include accounts for which status is Active
#### Create a difference column holding the difference between the account balance (amount) and the average balance for accounts of the same type to the right of all the other columns
#### The query should return the following columns from the account table: id, type, status, amount, as well as the difference calculated
```
SELECT A.id,
       A.type,
       A.status,
       A.amount,
       (A.amount-B.avg_amt) AS difference
  FROM account A
  JOIN ( SELECT type ,
                AVG(amount) AS avg_amt
           FROM problem1.account
          GROUP BY type
       ) B
    ON A.type = B.type
 WHERE A.status='Active'
```

## Problem 2
### Create an employee table in the metastore that contains the employee records stored in HDFS.
### All of the employee records are stored in the /user/training/problem2/data/employee/ HDFS directory in Parquet file format.
#### The table you create should be named solution and stored in the problem2 database
#### The table must point to the existing data in HDFS
```
CREATE DATABASE problem2;

CREATE EXTERNAL TABLE solution
(
    id INT,
    fname STRING,
    lname STRING,
    address STRING,
    city STRING,
    state STRING,
    zip STRING,
    birthday STRING,
    hireday STRING
)
    STORED AS PARQUET
    LOCATION '/user/training/problem2/data/employee'

SELECT * FROM solution;
```

## Problem 3
### Generate a table that contains all customers who have negative account balances.
### The customer records are stored in the customer table in the problem3 database. The account records are stored in the account table in the problem3 database. The account records contain a field called amount that may be negative. The custid field is a foreign key into the customer table.
#### Create a metastore table named solution stored in the problem3 database that contains the customer records that match the criteria
#### Each solution record should contain the Customer ID, first name, last name, and home phone of the customer
#### Maintain the same column names and datatypes in the new table as the fields from the customer table
```
CREATE TABLE solution AS
SELECT C.id, C.fname, C.lname, C.hphone
  FROM CUSTOMER C
  JOIN ACCOUNT A
    ON C.id = A.custid
 WHERE A.amount < 0;
```

## Problem 4
### LoudAcre Mobile has merged with another company located in California. Each company has a list of customers in different formats. Combine the two customer lists into a single dataset using an identical schema.
### The original customer data exists in the HDFS directory /user/training/problem4/data/employee1/. It contains expanded, nine-digit zip codes.
### The new files are in the HDFS directory /user/training/problem4/data/employee2/. It contains last names before first names, both using all capital letters.
#### Combine these files into a single tab-delimited dataset and stored in the HDFS directory /user/training/problem4/solution/
```
```

## Problem 5
### The bank is making a Facebook group for the Palo Alto, CA branch. Generate a script that outputs the customers and employees who live in Palo Alto, CA.
### The employee records are stored in the employee metastore table in the problem5 database, The customer records are stored in the customer metastore table in the problem5 database.
#### Write the report query in the local file /home/training/problem5/solution.sql
#### Executing the solution.sql script from the hive command-line tools should generate the report output
#### The report should contain first name, last name, city, and state with a tab as the delimiter between fields
#### Only output records that have a city value of ‘Palo Alto’ and state value of ‘CA’
#### Duplicate records (if any) should be included
```
SELECT A.fname, A.lname, A.city, A.state
  FROM customer A
 WHERE A.city = 'Palo Alto'
   AND A.state = 'CA'
 UNION ALL
SELECT B.fname, B.lname, B.city, B.state
  FROM employee B
 WHERE B.city = 'Palo Alto'
   AND B.state = 'CA'
```

## problem 6
### There are privacy concerns about the employee data that is stored on the cluster. Your task is to remove any age information from the employee data by creating a new table for the data analysts to query against.
### All of the employee records are stored in the employee metastore table in the problem6 database.
#### Create a new table named solution stored in the problem6 database with the same file format as the employee table
#### Maintain the same column names and datatypes in the new table
#### The birthday field in the solution table should be truncated to only contain month/day instead of the current month/day/year data that is in the employee table
```
CREATE TABLE solution
(
	  id INT,
	  fname STRING,
	  lname STRING,
	  address STRING,
	  city STRING,
	  state STRING,
	  zip STRING,
	  birthday STRING
)

INSERT INTO TABLE problem6.solution
SELECT id, fname, lname, address, city, state, zip,
	     substring(birthday, 0 ,5)
  FROM problem6.employee

SELECT * FROM solution;
```

## problem 7
### Generate a report that contains all of the Seattle employee names in sorted order.
### The employee records are stored in the employee table in the problem7 database.
#### Write the report query in the local file /home/training/problem7/solution.sql
#### Executing the solution.sql script from the hive command-line tools should generate the report output
#### The employee names should be printed out as the first name, a space, and the last name
#### The output should be sorted by first name and then by last name and should only contain employees whose city is ‘Seattle’
#### Duplicate names should be included (if any)
```
SELECT CONCAT(fname, ' ' , lname) AS name
  FROM employee
 WHERE city = 'Seattle'
 ORDER BY name
```

## problem 8
### Use Sqoop to export customer data from HDFS into a MySQL database table. Place the data in the solution table in MySQL, which has been created and is currently empty.
### The data files are in the HDFS directory /user/training/problem8/data/customer/. MySQL database information:
### Installation: localhost, Database name: problem8, Table name: solution, Username: cloudera, Password: cloudera
#### Export all of the customer data from HDFS into the MySQL solution table
#### The solution table already exists in the MySQL database but currently has no rows
```
sqoop export --connect jdbc:mysql://localhost/problem8 --username cloudera --password cloudera --export-dir /user/training/problem8/data/customer --table solution --input-fields-terminated-by '\t'
```

## problem 9
### Your company is being acquired by another company. To prepare for this acquisition, update the customer records to guarantee there will be no duplicate IDs with their existing customer IDs.
### The customer records are stored in the customer table in the problem9 database. The id column is a unique identifier for that record.
#### Create a new table named solution stored in the problem9 database
#### Maintain the same column names and datatypes as the customer table, except store the id as a string
#### The solution table should have all of the data from the customer table, with the addition of the letter ‘A’ to the existing id values to make them unique
```
CREATE TABLE solution
(
	  id STRING,
	  fname STRING,
	  lname STRING,
	  address STRING,
	  city STRING,
	  state STRING,
	  zip STRING,
	  birthday STRING
)

INSERT INTO TABLE solution
SELECT CONCAT('A', id),
	     fname,
	     lname,
	     address,
	     city,
	     state,
	     zip,
	     birthday
  FROM customer;

SELECT * FROM solution;
```

## problem 10
### Your boss needs specialized reports using the billing data and is constantly asking for help to write SQL queries. Create a database view in the metastore so that your boss has customer and billing data joined.
### The customer data exists in the customer metasore table in the problem10 database.
### The billing data exists in the billing metastore table in the problem10 database. The id column is a foreign key into which customer has the charge.
#### create a new view named solution stored in the problem10 database
#### The new view should maintain the same column datatypes as the source tables
#### Do not return customer records that have no billing data, or billing records that have no matching customer
#### The new view should contain the following columns
#### The billdate column should only contain the date field and no time information
```
CREATE VIEW solution AS
SELECT A.id,
       A.fname,
       A.lname,
       A.city,
       A.state,
       B.charge,
       substr(B.tstamp,1,10) AS billdata
  FROM customer A
  JOIN billing B
    ON A.id = B.id;

select * from solution;	
	
```

## problem 11
### Several analysis questions are described below and you will need to write the SQL code to answer them. You can use whichever tool you prefer – Impala or Hive – using whichever method you like best, including shell, script, or the Hue Query Editor, to run your queries.
### Using default database from the metastore for these queries.
#### Write the report query in the local file /home/training/problem11/solution.sql
#### Executing the solution.sql script from the hive command-line tools should generate the report output
##### a. Which top three products has Dualcore sold more of than any other
##### Hint: Remember that if you use a GROUP BY clause, you must group by all fields listed in the SELECT clause that are not part of an aggregate function.
```
SELECT A.brand,
       A.name,
       COUNT(A.prod_id) AS cnt
  FROM products A
  JOIN order_details B
    ON (A.prod_id = B.prod_id)
 WHERE A.brand = 'Dualcore'
 GROUP BY brand, name, A.prod_id
 ORDER BY cnt DESC
 LIMIT 3;
```
##### b. Calculating Revenue and Profit – write a query to show Dualcore’s revenue (total price of products sold) and profit (price minus cost) by date.
##### Hint: The order_date column in the orders table is of type TIMESTAMP. Use the function to_date to get just the date portion of the value.
```
SELECT TO_DATE(order_date) as date,
       SUM(price) AS revenue,
       SUM(price - cost) as profit
  FROM products A
  JOIN order_details B
    ON (A.prod_id = B.prod_id)
  JOIN orders C
    ON (B.order_id = C.order_id)
 WHERE A.brand = 'Dualcore'
 GROUP BY TO_DATE(order_date)
 ORDER BY date;
```
##### c. Calculating the order Total – Which ten orders had the highest total dollar amounts?
```
SELECT A.order_id,
       SUM(B.price) AS total
  FROM order_details A
  JOIN products B
    ON (A.prod_id = B.prod_id)
  GROUP BY order_id
  ORDER BY total DESC
  LIMIT 10;
```
