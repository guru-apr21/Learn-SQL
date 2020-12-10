# Learn-SQL
### Shorthand SQL commands
SQL commands are not case sensitive, but using uppercase is considered as best practice.

# Retrieving Data from Single Table

## SELECT CLAUSE - Retrieves columns from the table
```sql
SELECT * FROM tableName
```

## FROM CLAUSE - Specifies the table name 
```sql
SELECT * FROM tableName
```
## WHERE CLAUSE - Used along with various operators to filter the retrieved data
```sql
SELECT * FROM tableName WHERE columnName > 20
```
## AND, OR, NOT - Logical operators used to write multiple conditions
```sql
SELECT * FROM tableName 
  WHERE columnName > 20 AND columnName="sometext"
``` 
## LIKE Operator - To retrieve rows that match a specific string pattern
* % - To represent any number of characters, also not case sensitive
* _ - To represent single character
```sql
SELECT * FROM tableName 
  WHERE firstName LIKE "%y" OR phone "98%"
```

## REGEXP Operator - To match any string pattern
It is more powerfull than using LIKE operator. REGEXP operator is a new addition to SQL
* ^ beginning
* $ end
* | logical OR
* [] to match any characters listed in brackets
* [a-z] to match a range of characters

## LIMIT CLAUSE - Used to limit the number of records returned from the query
```sql
SELECT * FROM tableName 
  WHERE columnName > 20 AND columnName="sometext"
  LIMIT 3
```
Optionally we can provide a offset which is usefull when we want to paginate the data
```sql
SELECT * FROM tableName 
  WHERE columnName > 20 AND columnName="sometext"
  LIMIT 6,3
```
This command skips the first 6 records and then pick 3 records 

## IS NULL 
Returns only the column with null value
```sql
SELECT * FROM tableName 
  WHERE columnName IS NULL
```

# Retrieving data from multiple tables

## Inner Join - Used to join two different table data
 ```sql
 SELECT * 
  FROM customers 
  JOIN orders
  ON orders.customer_id = customers.customer_id
 ```
 Here the ON phrase defines on what basis we want to combine the table. Provide a condition after the ON phrase.  
 So with this query we are telling mySQL whenever you join orders table with the customers table make sure the customer_id column in the orders table equals customer_id column in the customers table.
 
  ```sql
 SELECT first_name,last_name,customers.customer_id 
 	FROM customers 
	JOIN orders 
	ON orders.customer_id = customers.customer_id
 ```
 If you want to retrieve a column that is present is both the tables make sure you prefix the column name with either one of the table names.  
 Otherwise sql will throw an error.
  ```sql
 SELECT * 
 	FROM customers c 
	JOIN orders o 
	ON o.customer_id = c.customer_id
 ```
 Alternatively we can use alias instead using the entire table names.

 ### Joining across Databases
```sql
USE sql_inventory;

SELECT * 
	FROM sql_store.order_items oi 
	JOIN products p 
	ON oi.product_id=p.product_id
```
To join tables across different databases prefix the table with the database name.
Here too I am usig alias.

### Self Join
```sql
USE sql_hr;

SELECT e.employee_id, e.first_name, m.first_name AS manager 
	FROM employees e 
    	JOIN employees m 
    	ON e.reports_to = m.employee_id
```
To join a table with itself we use Self joins. The employee table has a column reports_to which is the id of the manager he/she reports to.  
This query joins the table with itself and selects the employee name and his manager. Use different alias for the tablename
