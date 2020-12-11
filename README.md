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
SELECT *
    FROM tableName
    WHERE columnName > 20
```

## AND, OR, NOT - Logical operators used to write multiple conditions

```sql
SELECT *
    FROM tableName
    WHERE columnName > 20 AND columnName="sometext"
```

## LIKE Operator - To retrieve rows that match a specific string pattern

- % - To represent any number of characters, also not case sensitive
- \_ - To represent single character

```sql
SELECT *
    FROM tableName
    WHERE firstName LIKE "%y" OR phone "98%"
```

## REGEXP Operator - To match any string pattern

It is more powerfull than using LIKE operator. REGEXP operator is a new addition to SQL

- ^ beginning
- $ end
- | logical OR
- [] to match any characters listed in brackets
- [a-z] to match a range of characters

## LIMIT CLAUSE - Used to limit the number of records returned from the query

```sql
SELECT *
    FROM tableName
    WHERE columnName > 20 AND columnName="sometext"
    LIMIT 3
```

Optionally we can provide a offset which is usefull when we want to paginate the data

```sql
SELECT *
    FROM tableName
    WHERE columnName > 20 AND columnName="sometext"
    LIMIT 6,3
```

This command skips the first 6 records and then pick 3 records

## IS NULL

Returns only the column with null value

```sql
SELECT *
    FROM tableName
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
Here too I am using alias.

### Self Join

```sql
USE sql_hr;

SELECT e.employee_id, e.first_name, m.first_name AS manager
	FROM employees e
    JOIN employees m
    	ON e.reports_to = m.employee_id
```

To join a table with itself we use Self joins. The employee table has a column reports_to which is the id of the manager he/she reports to.  
This query joins the table with itself and selects the employee name and his manager. Use different alias for the tablename.  
Since every column in the employee table is repeated twice we need to prefix each column with table name.

### Joining Multiple Tables

```sql
USE sql_store;
SELECT o.order_id, o.order_date, c.first_name, os.name as status
	FROM orders o
    JOIN customers c
    	ON o.customer_id = c.customer_id
    JOIN order_statuses os
    	ON os.order_status_id = o.status
```

```sql
USE sql_invoicing;
SELECT p.payment_id, c.name, p.date, p.amount, pm.name as payment_method
	FROM payments p
    JOIN clients c
    	ON p.client_id = c.client_id
    JOIN payment_methods pm
    	ON p.payment_method = pm.payment_method_id

```

We can use multiple JOIN phrase to join multiple tables and don't forget to prefix the column name with their respective table names or alias.  
In a real world problem there may be situations where we join more than 10 tables.

### Compound join conditions

```sql
SELECT *
FROM order_items oi
JOIN order_item_notes oin
	ON oi.order_id = oin.order_id
    AND oi.product_id = oin.product_id
```

When there is two primary key in a table it is known as composite primary keys.  
To join two tables based on two columns we use compound join conditions. We have multiple condition to join these two tables.

### Implicit Join syntax

```sql
SELECT *
    FROM orders o
    JOIN customers c
	    ON o.customer_id = c.customer_id;

SELECT *
    FROM orders o, customers c
    WHERE o.customer_id = c.customer_id;
```

Both syntax are equivalent. This is what we call implicit join syntax, the one without the JOIN phrase.  
Eventhough the both qyery return the same result it is always better to use explicit join syntax that is using JOIN phrase.  
Because In case if we forget adding WHERE clause it will result in a cross join.

## Outer Join

The Inner keyword is optional. So whenever you are using a JOIN you are using a inner JOIN.

```sql
SELECT c.customer_id, c.first_name, o.order_id
	FROM customers c
    	JOIN orders o
		ON c.customer_id = o.customer_id
	ORDER BY c.customer_id
```

This query returns only the customers who has placed an order. What if we want to see all the customers whether they have a order or not.  
That is when we use an Outer JOIN. In SQL there are two types of Outer JOINS.

- LEFT JOIN
- RIGHT JOIN

```sql
SELECT c.customer_id, c.first_name, o.order_id
	FROM orders o
		LEFT JOIN customers c
		ON c.customer_id = o.customer_id
	ORDER BY c.customer_id
```

When we use a LEFT JOIN all the records from the left table in this case orders will be returned whether the ON condition is true or not.  
Similary for the RIGHT JOIN. You cann swap the order of the table as you wish.
We don't have to explicitly type RIGHT OUTER JOIN or LEFT OUTER JOIN. It's optional and more of a personal preference.

### Outer JOIN Between Multiple Tables

Similar like inner joins we can use outer joins between multiple tables.

```sql
SELECT c.customer_id, c.first_name, o.order_id, s.name
	FROM customers c
    	LEFT JOIN  orders o
		ON c.customer_id = o.customer_id
	LEFT JOIN shippers s
		ON s.shipper_id = o.shipper_id
	ORDER BY c.customer_id
```

```sql
SELECT o.order_date, o.order_id, c.first_name, s.name AS shipper, os.name as status
	FROM orders o
    	JOIN customers c
		ON o.customer_id = c.customer_id
	LEFT JOIN shippers s
		ON s.shipper_id = o.shipper_id
	JOIN order_statuses os
		ON o.status = os.order_status_id
```

This query combines multiple tables and returns the records even if some of it's column contains null value.  
As a best practice always use LEFT JOIN so that it'll would be easy to visualize what we are doing.

### Self Outer JOIN

Similar like inner self joins we can use outer self joins.

```sql
SELECT e.employee_id, e.first_name , m.first_name as manager
	FROM employees e
	LEFT JOIN employees m
		ON e.reports_to = m.employee_id
```

To join a table with itself we use Self joins. The employee table has a column reports_to which is the id of the manager he/she reports to.  
This query joins the table with itself and selects the employee name and his manager. Use different alias for the tablename.  
Since every column in the employee table is repeated twice we need to prefix each column with table name.  
To retrieve the record of the manager who doesn't report to anyone we use LEFT OUTER JOIN.

### USING clause

```sql
SELECT o.order_id, c.first_name
	FROM orders o
    	JOIN customers c
		ON c.customer_id = o.customer_id
```

```sql
SELECT o.order_id, c.first_name
	FROM orders o
    	JOIN customers c
		USING (customer_id)
```

As you can see two queries are equivalent to each other and provides exact same result.  
If the column name are exactly same across tables we can replace ON clause with USING caluse which shorter and simpler.
Type out USING and in paranthesis we type out the column name. Also the USING keyword only works if the column name are same across different tables.

```sql
SELECT *
FROM order_items as oi
JOIN order_item_notes as oin
	ON oi.order_id = oin.order_id
    AND oi.product_id = oin.order_id
```

```sql
SELECT *
FROM order_items as oi
JOIN order_item_notes as oin
	USING(order_id, product_id)
```

Also if we want to join tables with multiple conditions pass the column names inside the paranthesis.

### Natural JOIN

```sql
SELECT o.order_id, c.first_name
	FROM customers c
	NATURAL JOIN orders o
```

With natural joins we don't explicitly specify the table names, So the database engine will look up to these two tables and join them based on the common columns.  
Sometimes it produces unexpected results because we don't have control over it.

### CROSS JOIN

```sql
SELECT p.name AS products, c.first_name AS customer
	FROM customers c
	CROSS JOIN products p
```

We use cross join to combine every record from the first table with the every record in the second table.
A real example for using cross joins is where you have table of sizes S, M, L and table of colors like White, Red, Yellow and  
then you want to combine all the sizes with all the colors.  
What we have here is the explicit way of using cross joins, insted we can do something like this-

```sql
SELECT p.name AS products, c.first_name AS customer
	FROM customers c, products p
```

Both the queries returns the same results.

## UNION operator

In SQL we use unions to combine rows from multiple tables

```sql
SELECT order_id, order_date, "Active" as status
	FROM orders
	WHERE order_date >= "2019-01-01"
UNION
SELECT order_id, order_date, "Archived" as status
	FROM orders
	WHERE order_date < "2019-01-01"
```

```sql
SELECT customer_id, first_name, points, "Bronze" AS type
	FROM customers
	WHERE points < 2000
UNION
SELECT customer_id, first_name, points, "Silver" AS type
	FROM customers
	WHERE points BETWEEN 2000 AND 3000
UNION
SELECT customer_id, first_name, points, "Silver" AS type
	FROM customers
	WHERE points > 3000
	ORDER BY first_name
```

Using the UNION operator we can combine the records from multiple queries. The number of columns each queries return should be equal.  
Otherwise SQL will throw an error. The name of the column will be based on the first query.

# Inserting, Updating and Deleting Data

## Column Attributes

 VARCHAR is shortname for variable characters. Inside the paranthesis we specify the maximum value the column can take.  
 For example if the customer first_name is five characters long, we only store those five characters eventhough the max value is 50.  
 In contrast we have CHAR datatype, here if the customer name is only five characters long MYSQL will insert additional 45 empty spaces to fill this column.  
 This is a waste of space. So as a best practice we use VARCHAR to store string or textual value.

## Inserting a Single Row 

```sql
INSERT INTO customers
VALUES (
    DEFAULT,
    "Guru",
    "Prakash",
    "1999-04-21",
    NULL,
    "Kovilpatti",
    "Tuticorin",
    "TN",
    DEFAULT
)
```
We use INSERT INTO statement and next the table name where we want to insert followed by the VALUES clause.  
In paranthesis we supply values for column in the table, by providing the DEFAULT keyword SQL will take care of generating the value.  
For eg the auto increment of customer_id which uniquely identifies every customer.

```sql
INSERT INTO customers (first_name, last_name, birth_date, city, address, state)
VALUES (
    "Guru",
    "Prakash",
    "1999-04-21",
    "Tuticorin",
    "Kovilpatti",
    "TN"
)
```

This is another way to write this query. After the table name we can optionally supply the list of columns that we want to insert values into.  
With this change we dont have to supply default or null values. With this change we can re-order the column we dont have to list them in the same order as they are defined in the table.
