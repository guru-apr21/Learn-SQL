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

## Inserting Multiple Rows

```sql
INSERT INTO shippers(name)
VALUES  ("shipper1"),
	("shipper2")
```

```sql
INSERT INTO products(name, quantity_in_stock, unit_price)
VALUES  ("Panner", 5, 48.11),
	("Chesse", 4, 96.16),
        ("Soda", 7, 79.87)
```

To insert multiple rows in one go all you have to do is add a comma next to the paranthesis followed by another pair of paranthesis.

## Inserting Hierarchial Rows

In our database a parent child relationship is established between orders and order_items table. The actual order items for each orders are in the order_items table which we can uniquely identify using the combination of product_id and order_id. So an actual order can have one or more order items. Orders table is the parent and order_items table is the child. One row in orders table can have one or more children in order_items table.

```sql
INSERT INTO orders (customer_id, order_date, status)
	VALUES (3,"2020-01-21",1);

INSERT INTO order_items
	VALUES
		(LAST_INSERT_ID(), 2, 3, 41.89),
		(LAST_INSERT_ID(), 4, 1, 36.7)
```

In MYSQL we have bunch of functions and function is basically a piece of code which we can reuse.  
In this query as soon as we create an order, an order_id will be generated then we can retrieve that order_id calling LAST_INSERT_ID function.  
Now we know the id of the parent record we can use that id to insert the parent record. Since we need to fill every column there is no need to specify the column name in the INSERT statement.

## Creating A Copy Of A Table

```sql
CREATE TABLE orders_archived AS
	SELECT * FROM orders
```

This query will create a new table with all the data in the orders table. But this new table do not have a primary key.  
When we create a new table using this technique MYSQL will ignore few column attributes. We refer the above SELECT statement as a Sub query.  
A Sub query is a SELECT statement that is part of another SQL statement. We can also use Sub query in a INSERT statement

```sql
INSERT INTO orders_archived
SELECT *
FROM orders
WHERE order_date < "2019-01-01"
```

This query will only returns the orders that are placed before 2019. The SELECT statement is used as a Sub query.

```sql
USE sql_invoicing;
CREATE TABLE invoices_archived AS
SELECT i.invoice_id,
	i.number,
	c.name AS client_name,
	i.invoice_total,
	i.payment_total,
	i.invoice_date,
	i.due_date,
	i.payment_date
FROM invoices i
JOIN clients c
	USING(client_id)
WHERE i.payment_date IS NOT NULL
```

This query creates a new table invoices_archived and copy the invoices that do have a payment

## Updataing a Single Row

```sql
UPDATE invoices
SET payment_total = 78.99, payment_date = "2020-12-05"
WHERE invoice_id = 1
```

We use UPDATE statement to update one or more records in the table. Next we use SET clause this is where we specify new values for one or more columns.  
We use comma to add more columns.

```sql
UPDATE invoices
SET payment_total = DEFAULT, payment_date = NULL
WHERE invoice_id = 1
```

We can use the NULL keyword to insert the null value in a column that accepts NULL value.

```sql
UPDATE invoices
SET payment_total = invoice_total*0.4, payment_date = due_date
WHERE invoice_id = 3
```

We can also use expressions when setting the column value. Here the client paid 40 percent of the invoice_total on due_date

## Updating Multiple Rows

```sql
UPDATE invoices
SET payment_total = invoice_total*0.4, payment_date = due_date
WHERE client_id = 1
```

To update multiple rows the syntax is exactly same as updating single rows but the condition should be more general.  
This query will update every row with client_id 1

```sql
UPDATE invoices
SET payment_total = invoice_total , payment_date = due_date
WHERE client_id IN (2,3)
```

Here we can also use an IN operator to specify the client_id. All the operators that can be used with WHERE clause are valid here.

```sql
USE sql_store;
UPDATE customers
SET points = points + 100
WHERE birth_date < "1990-01-01"
```

This query adds 50 extra points to any customers born before 1990.

## Using Subqueries in Updates

```sql
USE sql_invoicing;
UPDATE invoices
SET payment_total = invoice_total,
	payment_date = invoice_date
WHERE client_id =
		(SELECT client_id FROM clients
		WHERE name = "Yadel")
```

In the above query SELECT statement is a sub query in a update statement.  
As I told before a Sub query is a SELECT statement that is part of another SQL statement. Here we are the sub query to find the client_id by his/her name. So MYSQL will execute the sub query first since it is in a paranthesis and returns the client_id.

```sql

USE sql_invoicing;
UPDATE invoices
SET payment_total = invoice_total,
	payment_date = invoice_date
WHERE client_id IN
		(SELECT client_id FROM clients
		WHERE state IN ("CA","NY"))
```

If the sub query return multiple values we use IN operator in the WHERE clause.  
As a best practice before updating a table run your query to see what data you are gonna update.

```sql
USE sql_store;
UPDATE orders
SET comments = "Gold"
WHERE customer_id IN
		(SELECT customer_id FROM customers
		WHERE points > 3000)
```

This query updates the comments for orders for customers who have more than 3000 points. If they had placed an order update the comment as gold.

## DELETING ROWS

```sql
DELETE FROM invoices
WHERE invoice_id = 1
```

We use the DELETE FROM statement to delete records from the table. Optionally we can add search condition to identify the records that we want to delete.  
If we dont provide a WHERE clause with this statement we delete all the records in the table.

```sql
USE sql_invoicing;
DELETE FROM invoices
WHERE client_id =
	(SELECT client_id FROM clients
	WHERE name = "Myworks")
```

We can also use a sub-query along with DELETE statement.

# Summarizing DATA

## Aggregate Functions

Function is a piece of code that we can reuse. MySQL comes with a bunch of built-in functions some of these functions are known as aggregate functions.  
Because they take a series of values and aggregate them to produce a single value.

- MAX() - To calculate the maximum of the series of values
- MIN() - To calculate the minimum of the series of values
- AVG() - To calculate the average of the series of values
- SUM() - To calculate the sum of the series of values
- COUNT() - To calculate the total count of the series of values

As you can see we need to use paranthesis to call or execute them.

```sql
SELECT
	MAX(invoice_total) AS highest,
	MIN(invoice_total) AS lowest,
    	AVG(invoice_total) AS average,
    	SUM(invoice_total) AS sum,
    	COUNT(invoice_total) AS number_of_invoices
FROM invoices
```

In this query I am applying these functions on a column that has a numeric values but we can apply them in date and strings too.

```sql
SELECT
    COUNT(*) AS total_records,
    COUNT(DISTINCT client_id) AS total_clients
FROM invoices
```

Aggregate functions operates on only non-null values. If we have a null value in the columns it is not going to be included in the function.
If you want to get the total number of records in the table irrespective of the non-null values. Use \* as a argument to the COUNT function.  
By default all these aggregate functions take duplicate values. If you want to exclude duplicate values you'll have to use DISTINCT keyword.

```sql
SELECT
	"First half of 2019" AS date_range,
    	SUM(invoice_total) AS total_sales,
    	SUM(payment_total) AS total_payments,
    	SUM(invoice_total - payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date
	BETWEEN "2019-01-01" AND "2019-06-30"
UNION
SELECT
	"Second half of 2019" AS date_range,
    	SUM(invoice_total) AS total_sales,
   	SUM(payment_total) AS total_payments,
    	SUM(invoice_total-payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date
	BETWEEN "2019-07-01" AND "2019-12-31"
UNION
SELECT
	"Total" AS date_range,
    	SUM(invoice_total) AS total_sales,
    	SUM(payment_total) AS total_payments,
    	SUM(invoice_total-payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date
	BETWEEN "2019-01-01" AND "2019-12-31"
```

## GROUP BY clause

```sql
SELECT
	SUM(invoice_total) AS total_sales
FROM invoices i
GROUP BY client_id
```

We use GROUP BY clause to group date by one or more columns. Right after the from clause we use the GROUP BY clause followed by one or more columns for grouping data.  
The GROUP BY clause should be placed after the FROM and WHERE clauses and before the ORDER BY clause. Otherwise SQL will throw an error.

```sql
SELECT
    	city,
    	state,
	SUM(invoice_total) AS total_sales
FROM invoices i
JOIN clients c
	USING(client_id)
GROUP BY state, city
```

In this query data is grouped by multiple columns. This results in total sales for each state and city combination

```sql
SELECT
	p.date,
    	pm.name AS payment_method,
    	SUM(p.amount) AS total_payments
FROM payments p
JOIN payment_methods pm
	ON p.payment_method = pm.payment_method_id
GROUP BY p.date, pm.name
ORDER BY p.date
```

This query generates a report that has three columns date, payment_method and total payments. Returns the total payment for each date and payment method combination.

## HAVING clause

```sql
SELECT
	client_id,
	SUM(invoice_total) AS total_sales
FROM invoices
GROUP BY client_id
HAVING total_sales > 400
```

This query returns only the clients who have total sales more than 400 dollars.  
Here we cannot use WHERE clause because WHERE clause executes before the grouping of data. That's why we use the HAVING clause which filters data after we group our rows.  
With WHERE clause we can group the data before our rows are grouped and with the HAVING clause we can filter the data after our rows are grouped.

```sql
SELECT
	client_id,
	SUM(invoice_total) AS total_sales,
    	COUNT(*) AS number_of_invoices
FROM invoices
GROUP BY client_id
HAVING total_sales > 500 AND number_of_invoices > 5
```

This query returns clients having total sales greater than 500 and number of invoices greater than 5. The columns that we use in HAVING clause have to be part of the SELECT clause.

```sql
SELECT
	c.customer_id,
	c.first_name,
	c.state,
    	SUM(oi.quantity * oi.unit_price) AS amount_spent
    	FROM customers c
JOIN orders o
	USING(customer_id)
JOIN order_items oi
	USING(order_id)
WHERE c.state = "VA"
GROUP BY c.customer_id,
	 c.first_name,
	 c.state
HAVING amount_spent > 100

```

This query returns the customers located in Virginia who have spent more than $100.  
As a rule of thumb whenever you have a aggregate function in SELECT statement, when grouping your data you should group by all the columns in the SELECT clause.

## The ROLLUP operator

```sql
SELECT
	client_id,
	SUM(invoice_total) AS total_sales
FROM invoices
GROUP BY client_id WITH ROLLUP
```

In MYSQL we have a powerfull operator to summarize data known as WITH ROLLUP. The ROLLUP operator applies only to the columns that aggregate values.  
We can also ROLLUP operator when grouping by multiple columns. It is only availabe in MYSQL and not part of the standard SQL language.

```sql
USE sql_invoicing;
SELECT
	pm.name AS payment_method,
    SUM(p.amount) as total
FROM payments p
JOIN payment_methods pm
	ON p.payment_method = pm.payment_method_id
GROUP BY pm.name WITH ROLLUP
```

When we use the ROLL UP operator we cannot use the column alias in the GROUP BY clause. So we nned to type the actual name of the column.

# Writing Complex Query

## SubQueries

```sql
SELECT * FROM employees
WHERE salary >
	(SELECT AVG(salary) FROM employees)
```

This query returns the employees whose salary is greater than the average.  
The query within the paranthesis will get executed first whose value is used to compare the salaries.

## IN Operator

```sql
SELECT * FROM products
WHERE product_id NOT IN
	(SELECT product_id FROM order_items)
```

Using IN operator following the WHERE clause and before the subQuery.  
This query returns products that never been ordered. We are using the NOT operator to retrieve the product that never been ordered.

```sql
SELECT * FROM clients
WHERE client_id NOT IN
	(SELECT client_id FROM invoices )
```

This query returns clients who have no invoices.  
We need to get unique list of clients from the invoices table and we use that to find who don't exist in this list.

## Subqueries Vs Joins

```sql
SELECT * FROM clients
LEFT JOIN invoices
	USING(client_id)
WHERE invoice_id IS NULL
```

```sql
SELECT * FROM clients
WHERE client_id NOT IN
	(SELECT client_id FROM invoices )
```

Here the problem that we are trying to solve is to retrieve the clients who don't have a invoice.  
We can use either use Subquery or Outer Join to solve this problem. The choice is ours. What more important is the readability of our code.

```sql
SELECT *
FROM customers c
WHERE customer_id IN
	(SELECT customer_id FROM orders
	JOIN order_items USING(order_id)
	WHERE product_id=3)
```

```sql
SELECT *
FROM customers c
JOIN orders
	USING(customer_id)
JOIN order_items
	USING(order_id)
WHERE product_id=3
```

Both the queries returns the customer who have ordered product with id 3. The first one uses a SubQuery whereas the second one uses JOIN to retrieve the result.

## The ALL keyword

```sql
SELECT *
FROM invoices
WHERE invoice_total >
	(SELECT MAX(invoice_total)
	FROM invoices
	WHERE client_id = 3)
```

Here we are subQuery to find the maximum of all invoices made by client with id and using that value we are retreiving all the invoices greater than the maximum value.  
We can rewrite this query using the ALL keyword.

```sql
SELECT * FROM invoices
WHERE invoice_total > ALL
	(SELECT invoice_total
	FROM invoices
	WHERE client_id = 3)
```

In this query we remove the MAX function and prefixed the subQuery with the ALL keyword.  
The subquery here returns multiple value and MYSQL will compare each value with the invoice_total. Both the queries will return exact same results.

## The ANY keyword

```sql
SELECT *
FROM clients
WHERE client_id IN
	(SELECT client_id
	FROM invoices
	GROUP BY client_id
	HAVING COUNT(*)>=2)
```

This query returns clients with at least two invoices. Also here we are using a SubQuery. Instead of the IN operator we can use ANY operator.

```sql
SELECT *
FROM clients
WHERE client_id = ANY
	(SELECT client_id
	FROM invoices
	GROUP BY client_id
	HAVING COUNT(*)>=2)
```

Here we use ANY operator prefixed by the equal to operator this means "equal to any of the returned values from the subqueries".  
Both the queries return exactly same results.

## Correlated Subqueries

```sql
SELECT * FROM employees e
WHERE salary > (
SELECT
	AVG(salary) as average
FROM employees
WHERE office_id=e.office_id
)
```

Here we are correlated subQuery that is we are referencing the table alias of the outer query inside the subQuery.  
So the subquery will get executed for each employee record. This may cause performance issue sometimes.  
In contrast we have un correlated sub queries where the subquery will get executed only once.

```sql
SELECT *
FROM invoices i
WHERE invoice_total >(
	SELECT AVG(invoice_total)
	FROM invoices
	WHERE client_id = i.client_id
)
```

This query also uses a correlated subQuery where it reference of the table alias of the outer query.  
It results in clients with invoice total greater than his average.

## The EXISTS operator

```sql
SELECT * FROM clients
WHERE client_id IN (
	SELECT DISTINCT client_id
	FROM invoices
	WHERE invoice_id IS NOT NULL)
```

This query returns the list of clients who have an invoice and we are using a subquery here. We can also use a Inner JOIN to execute this query.  
Yet another way to execute this query is using the EXISTS operator.

```sql
SELECT * FROM clients c
WHERE EXISTS (
	SELECT DISTINCT client_id
	FROM invoices
	WHERE client_id=c.client_id)
```

In this query right after the WHERE caluse I have used a EXISTS operator and as you can see here the subQuery is a correlated SubQuery, it references the table alias of the outer table. Hence the subQuery will get executed for each client.  
While using the EXISTS operator the subquery does not return a list instead it returns a boolean during each execution. In this case it checks whether it matches the given criteria. For example let's imagine that we have a million of client data, so when using IN operator along with a SUB query it returns a huge list which causes performance issues. So use EXISTS operator in those situations.

```sql
SELECT * FROM products p
WHERE NOT EXISTS(
	SELECT DISTINCT product_id from order_items
	WHERE product_id = p.product_id
)
```

This query returns the products that have never been ordered.

## Subqueries in the SELECT clause

```sql
SELECT
	invoice_id,
	invoice_total,
    	(SELECT AVG(invoice_total) FROM invoices) AS invoice_average,
    	invoice_total - (SELECT invoice_average) AS difference
FROM invoices
```

Sub queries are not limted to the WHERE caluse we can also use it in SELECT clause and FROM clause. In this query we are using subqueries to return the invoice average and difference between the invoice total and invoice average.  
In the difference we cannot use a table alias in a subQuery so we are using a SELECT clause and referencing that subQuery.

```sql
SELECT
	c.client_id,
    	c.name,
    	(SELECT SUM(invoice_total) FROM invoices WHERE client_id = c.client_id ) AS total_sales,
    	(SELECT AVG(invoice_total) FROM invoices) as average,
    	(SELECT total_sales - average) AS difference
FROM clients c
```

This query returns the sales information for each client.

## Subqueries in the FROM clause

```sql
SELECT * FROM
	(SELECT
		c.client_id,
		c.name,
		(SELECT SUM(invoice_total) FROM invoices WHERE client_id = c.client_id ) AS total_sales,
		(SELECT AVG(invoice_total) FROM invoices) as average,
		(SELECT total_sales - average) AS difference
	FROM clients c) AS sales_summary
WHERE total_sales IS NOT NULL
```

In this query I am using a subQuery in the WHERE clause.  
When we use a sub_query in the WHERE clause we should give a alias which is mandatory.  
This query returns the sales summary for each client where the total sales is not null.

# Essential MYSQL functions

## Numeric functions

MYSQL have built in functions to work with numeric values. Some of them are 

* ROUND()   - for rounding a number. This function has a optional second argument which we can use to specify the precision for rounding.
* CEILING() - returns the smallest integer that is greater than or equal to this number.
* FLOOR()   - returns the greatest integer that is less than or equal to this number.
* ABS()     - for calculating the absolute value of a function. It always returns a positive value
* RAND()    - for generating random floating point number between 0 and 1.

[Other functions for working with numeric data can be found here.](https://dev.mysql.com/doc/refman/8.0/en/numeric-functions.html)

## STRING functions

* LENGTH()    - to get number of characters in string.
* UPPER()     - for converting a string into uppercase.
* LOWER()     - for converting a string into lowercase.
* LTRIM()     - removes extra spaces before the string.
* RTRIM()     - removes extra spaces after the string.
* TRIMM()     - to remove any leading or trailing spaces.
* LEFT()      - to return something from left side of the string. Second argument is the number of string to retrieve.
* RIGHT()     - to return something from right side of the string. Second argument is the number of string to retrieve.
* SUBSTRING() - to get few character from anywhere in the string. Second argument is the start position and the third argument is the length. Third argument is optional.
* LOCATE()    - returns the first occurence of the character or sequence of characters. The first argument is the search string and the second argument is the actual string. The search string is not case sensitive. If the search string doesn't exist in the string it return 0 which is different from most programming languages.
* REPLACE()   - to replace a character or a sequence of characters. The second argument is what we want to replace and the third argument is with what we want to replace.
* CONCAT()    - used to concatinate two strings.

[Other functions for working with string data can be found here.](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html)

## DATE functions

* NOW() - to get current date and time
* CURDATE() - return current date without the time component
* CURTIME() - return current time

SQL have some functions to exatract the certain component from date and time

* YEAR(NOW()) - returns the current year
* MONTH(NOW()) - returns the current month 
* DAY(NOW()) - returns the current day
* HOUR(NOW()) - returns the current hour
* MINUTE(NOW()) -returns the current minute

All these function returns the integer values but we have two usefull functions which returns a string.

* DAYNAME(NOW()) - returns the day of the week as a string
* MONTHNAME(NOW()) - returns the month as a string

Apart from these functions we have EXTRACT function which is part of the standard SQL language. 

```sql
SELECT EXTRACT(DATE FROM NOW())
```
When using extract function we type the value that we want to extract followed by the FROM keyword and then the NOW() function.

## Formatting Dates and Times

In MYSQL we have couple of functions for formatting dates and times in a more user-friendly way.

```sql
SELECT DATE_FORMAT(NOW(),"%M %d %Y")
```
Here we have a function DATE_FORMAT which takes the current date and then a format string which contains special codes for formatting various parts of the date.
* %M - returns the name of the month.
* %m - returns the two digits month.
* %y - displays two digit year.
* %Y - displays four digit year.
* %d - returns two digit date

[Here is the reference for format string options.](https://www.w3schools.com/sql/func_mysql_date_format.asp)

```sql
SELECT TIME_FORMAT(NOW(),"%H:%i %p")
```
Here we have a function TIME_FORMAT which takes the current time and then a format string which contains special codes for formatting various parts of the time.

* %H - returns the hour in two digits
* %i - returns the minutes in two digits
* %p - specifies whether it is an AM or PM.

## Calculating Dates and Times.

MYSQL have built-in functions for performing calculations on dates and times.

```sql
SELECT DATE_ADD(NOW(), INTERVAL 1 DAY)
```
Adds a date part to a date time value. As a first argument we pass current date and time. As a second argument we pass a expression.  
This query returns the tomorrow's date with same time. We can also return next year with current date time by simply changing the date to year.

```sql
SELECT DATE_ADD(NOW(), INTERVAL 1 YEAR)
```
To go back in time we can either pass a negative value in the second argument or we can either pass a DATE_SUB function.
```sql
SELECT DATE_ADD(NOW(), INTERVAL -1 DAY)
```
```sql
SELECT DATE_SUB(NOW(), INTERVAL 1 YEAR)
```

We can also calculate the difference between two dates using DATEDIFF function
```sql
SELECT DATEDIFF("2020-12-05", "2020-11-30")
```
This function return the difference only in days. Not in hours or minutes.

Using TIME_TO_SEC function we can calculate the difference between two times and this returns the number of seconds elapsed since midnight.  
```sql
SELECT TIME_TO_SEC("09:02") - TIME_TO_SEC("09:00")
```
This returns difference between these two time in seconds.

## The IFNULL and COALESCE Functions.

```sql
SELECT 
	order_id, 
	IFNULL(shipper_id, "Not assigned") AS shipper
FROM orders
```
```sql
SELECT 
	CONCAT(first_name," ",last_name) AS customers,
    	IFNULL(phone, "Unknown") AS phone
FROM customers 
```
When using IFNULL function if the first argument is null it will be replaced by the string in the second argument.

Similarly we have another function called COALESCE which returns the first non null value in the list we passed as arguments.
```sql
SELECT SELECT 
	order_id, 
	COALESCE(shipper_id, comments, "Not assigned") AS shipper
FROM orders
```

## The IF function

*IF(expression, first, second)* If the expression we passed as a first argument evaluates to be true it returns the first value or else it returns the second value.

```sql
SELECT 
	order_id, 
	order_date, 
    	IF(YEAR(order_date)=YEAR(NOW()), "Active", "Archived") AS status
FROM orders
```
This query returns the orders that are placed in the current year as Active status or else as Archived.

```sql
SELECT 
	product_id,
    	name,
    	COUNT(*) AS orders,
    	IF(COUNT(*) > 1, "Many times", "once") AS frequency
FROM products 
JOIN order_items
	USING(product_id)
GROUP BY product_id, name
```

This query returns each product and the frequency of orders placed for that product.

## The CASE operator

Using IF function function we can test a expression and return different values depending upon the result of that expression.  
What if we have multiple expression to test and return different values, that's when we use a CASE operator.

```sql
SELECT 
	order_id,
	CASE 
    		WHEN YEAR(order_date) = YEAR(NOW()) THEN "Active"
    		WHEN YEAR(order_date) = YEAR(NOW()) - 1 THEN "Last Year"
    		WHEN YEAR(order_date) < YEAR(NOW()) - 1 THEN "Archived"
    		ELSE "Future"
    	END AS category
FROM orders
```
Here we type out case followed by one or more WHEN clauses. Each WHEN caluse includes a test expression next we add THEN, if the expression evaluates to true the value that we put after the THEN clause will be returned. After every test condition we can optionally add ELSE clause if none of the conditions evaluates to true the value given in the ELSE clause will be returned. Finally we have to close the CASE block with the END keyword.

```sql
SELECT 
	CONCAT(first_name, " ", last_name) AS customer,
    	points,
    	CASE
		WHEN points > 3000 THEN "Gold"
        	WHEN points >= 2000 THEN "Silver"
        	ELSE "Bronze"
        	END AS category
FROM customers
ORDER BY points DESC
```
This query returns the customers and their category based on the points they have scored.

# Views

Queries sometimes gets complex especially when using multiple joins and sub queries. We may need to use those queries in multiple places that's when views come to the rescue.  
We can save this queries in a view and resuse in a SELECT statement.

## Creating Views

```sql
CREATE VIEW sales_by_client AS
SELECT 
	c.client_id,
    	c.name,
    	SUM(i.invoice_total) AS total_sales
FROM clients c
JOIN invoices i
	USING(client_id)
GROUP BY c.client_id, c.name;
```

 To create a VIEW we use CREATE VIEW statement and the name of the view followed by the AS keyword, right after the AS we have our SELECT statement.  
 Executing this query creates a new VIEW object. We can use this view just like a table. 
 
 ```sql
SELECT * 
FROM sales_by_client
JOIN clients 
	USING(client_id)
 ```
 We can use JOIN to join with other tables with mutual columns also we can apply filter and many more. Views are extremely powerfull and it can simplify any future queries.  
 Views behaves like a virtual table but remember **Views don't store data.**. Our data is actually stored in the table. Our view just provides a view to the underlying table.
 
 ```sql
CREATE VIEW clients_balance AS 
SELECT 
	c.client_id,
    	c.name,
    	SUM(i.invoice_total - i.payment_total) AS balance
FROM clients c
JOIN invoices i
	USING(client_id)
GROUP BY c.client_id, c.name
 ```
 
 This query creates a view to see the balance for each client.

## Altering or Dropping Views

Once the view is created and if we want to change the query for that view. There is actually two ways to do it.  
The first one is using a DROP keyword which drops the view and then we can re-create it.

```sql
DROP view clients_balance
```
This query drops the view if there is one with the given name. Another way is to use a REPLACE keyword.

```sql
CREATE OR REPLACE VIEW clients_balance AS 
SELECT 
	c.client_id,
    	c.name,
    	SUM(i.invoice_total - i.payment_total) AS balance
FROM clients c
JOIN invoices i
	USING(client_id)
GROUP BY c.client_id, c.name
```
Here we are using the replace keyword which replaces the view if exists and creates a new one. This is the prefered approach because here we don't need to drop the view explicitly. We can store this query as a script file and put in a repository so that other people can use this view.  

```sql
CREATE 
    ALGORITHM = UNDEFINED 
    DEFINER = `root`@`localhost` 
    SQL SECURITY DEFINER
VIEW `sql_invoicing`.`clients_balance` AS
    SELECT 
        `c`.`client_id` AS `client_id`,
        `c`.`name` AS `name`,
        SUM((`i`.`invoice_total` - `i`.`payment_total`)) AS `balance`
    FROM
        (`sql_invoicing`.`clients` `c`
        JOIN `sql_invoicing`.`invoices` `i` ON ((`c`.`client_id` = `i`.`client_id`)))
    	GROUP BY `c`.`client_id` , `c`.`name`
```
MySQL surrounds your table and column names with the backtick characters and that is to prevent the name clash so if you use certain keywords that have speical meaning in SQL, MySQL will treat those just as view or column or table names.

## Updatable Views

So far we have seen that we can use our Views in SELECT statement but we can also use our Views in INSERT, UPDATE, DELETE statements but only under certain circumstances.

* DISTINCT
* Aggregate Functions (MIN, MAX, SUM, AVG...)
* GROUP BY / HAVING
* UNION

If the view does not have a DISTINCT keyword and aggregate functions, GROUP BY or HAVING clauses and finally UNION operator. If we don't use any of these stuffs in a view that view is known as an Updatable view which means that we can update data through it.

```sql
CREATE OR REPLACE VIEW invoices_with_balance AS
SELECT 
	invoice_id,
    	number,
    	client_id,
    	invoice_total,
    	payment_total,
    	invoice_total - payment_total AS balance,
	invoice_date,
    	due_date,
    	payment_date
FROM invoices
WHERE invoice_total - payment_total > 0
```

This query creates a view for the invoices table that includes the balance for each invoice. Here we are not using DISTINCT keyword, any aggregate function or GROUP BY neither we are using the UNION operator. So this is an Updatable View so we can use it to modify our DATA.

```sql
DELETE FROM invoices_with_balance
WHERE invoice_id = 1
```
This query deletes the invoice with id 1 in both the view and the underlying table. We can also update an invoice.

```sql
UPDATE invoices_with_balance
SET due_date = DATE_ADD(due_date, INTERVAL 3 DAY)
WHERE invoice_id = 2
```
This query updates the due date of the invoice with id 2. We can also insert a new invoice.  
But when inserting data our view must have all the record columns in the underlying table. If the view doesn't have a column which has not null attribute in the underlying table MYSQL will throw an error.  
Most of the times we update data through our tables but there are times that you might not have a direct permission to a table for security reasons and your only option is to modify data through the view only if your views are updatable.

## WITH CHECK OPTION clause

```sql
UPDATE invoices_with_balance
SET payment_total = invoice_total
WHERE invoice_id = 2
```

When executing the query the invoice with id 2 disappears.  
This is the default behaviour of views so when you update or delete data through view some of the rows might disappear. To prevent this from happening we add WITH CHECK OPTION clause at the very end after our SELECT statement when creating the VIEW.  

```sql
CREATE OR REPLACE VIEW invoices_with_balance AS
SELECT 
	invoice_id,
    	number,
    	client_id,
    	invoice_total,
    	payment_total,
    	invoice_total - payment_total AS balance,
	invoice_date,
    	due_date,
    	payment_date
FROM invoices
WHERE invoice_total - payment_total > 0
WITH CHECK OPTION
```
By applying this clause it'll prevent update or delete statements from excluding rows from the view. Now when we update something that may exclude data from the view MYSQL will throw an error.

# Stored Procedures

A stored procedure is a database object that contains a block of SQL code. In our application code we simply call these functions to get or save the data. 
We use stored procedures to store and organize our SQL code but it also has other benefits.  
Most DBMS perform some kind of optimization to the code in stored procedures. So the SQL code in stored procedure can sometimes be executed faster also just like views, stored procedures allow us to enforce data security.  
For example we can remove access to all the tables and allow various operations like inserting, updating and deleting data to be performed via stored procedures. Then we can decide who can execute which stored procedures and this will limit what the user can do with our data. For example we can prevent certain users from deleting our data.

## Creating a Stored Procedure

```sql
DELIMITER $$
CREATE PROCEDURE get_clients()
BEGIN
	SELECT * FROM clients;
END $$

DELIMITER ;
```
We can create a stored procedure using a CREATE PROCEDURE statement then we give our stored procedure a name after the name we add a pair of paranthesis.
Then we type BEGIN next to the BEGIN statement we have our query and then we terminate our stored procedure using the END keyword. 
What we have between the BEGIN and END statement is called the body of the stored procedure. 
In this query we have only one statement but in the real world the stored procedures that you create can have multiple statements so we have to terminate each statement with a semi colon even if we have a single statement. This is the MYSQL requirement.  
We have to give all these statement to the MYSQL as a single unit rather than individual statements separated using a semi colon. To make that happen we have to change the default delimeter semi colon to something else.  
By convention most developers use two $ signs but we can use any sequence of characters that we don't use in our SQL code. Then we need to add the new delimiter after the END statement. Now MYSQL will execute all of these commands as a single unit.  
Now finally we have to change the default delimeter back to a semi colon.

```sql
CALL get_clients()
```
We use the CALL statement to call or execute the stored procedure.

```sql
DELIMITER $$
CREATE PROCEDURE get_invoices_with_balance()
BEGIN
	SELECT *
	FROM invoices_with_balance
	WHERE balance > 0;
END$$
DELIMITER ;
```
Here we are creating a stored procedure with the name get_invoices_with_balance which when called returns the invoices with balance greater than 0.  
Also we using invoice_with_balance view in the body of the stored procedure.

## Dropping Procedures

```sql
DROP PROCEDURE get_invoices_with_balance
```

We use DROP PROCEDURE statement followed by the name of the procedure.  
If we execute this statement one more time we'll get an error because MYSQL doesn't allow us to drop a procedure that doesn't exist. 

```sql
DROP PROCEDURE IF EXISTS get_invoices_with_balance;

DELIMITER $$
CREATE PROCEDURE get_invoices_with_balance()
BEGIN
	SELECT *
	FROM invoices_with_balance
	WHERE balance > 0;
END$$
DELIMITER ;

```
To prevent the error we can use IF EXISTS keyword and it'll DROP the procedure only if it exists.  
As I said before it is a good practice to store the stored procedure code in a file and put it in a source control like git. So this is a basic template fo creating a stored procedure.

## Parameters

We use parameter to pass a value to a stored procedure but we can also use parameters to send value to the calling program.  

```sql
DELIMITER $$
CREATE PROCEDURE get_clients_by_state(state CHAR(2))
BEGIN
	SELECT *
	FROM clients c
	WHERE c.state = state;
END$$
DELIMITER ;
```
This stored procedure that we create here will take name of the state as a parameter and return the clients in that state.  
In between the paranthesis after the name of the stored procedure we define our parameter and set it's data type.
We can add multiple parameters to a stored procedure. In our query we compare the state column in the client table with the state parameter.
If we don't supply a value for the parameter we'll get an error. By default in MySQL all parameters are required.

```sql
DROP PROCEDURE IF EXISTS get_invoices_by_client;

DELIMITER $$
CREATE PROCEDURE get_invoices_by_client(client_id INT(11))
BEGIN
	SELECT * FROM invoices i
    	WHERE i.client_id = client_id;
END$$

DELIMITER ;
```
This stored procedure that we create here will take client id as a parameter and return the invoices for that client. 

## Parameters with Default Values

If the caller of our stored procedure doesn't provide a value to the parameter we need to set a default parameter.

```sql
DELIMITER $$
CREATE PROCEDURE get_clients_by_state(state CHAR(2))
BEGIN
	IF state IS NULL THEN
		SET state = "CA";
    	END IF;
    
	SELECT *
	FROM clients c
	WHERE c.state = state;
END$$
DELIMITER ;
```
Here before executing the SELECT statement we write an IF statement which evaluates the state and if it is null it sets "CA" as state parameter's default value.  
Whenever we use a IF statement we should always terminate with END IF. This is because we might have multiple statements after the IF statement so we need to tell MySQL where the IF statement ends.

```sql
DELIMITER $$
CREATE PROCEDURE get_clients_by_state(state CHAR(2))
BEGIN
	IF state IS NULL THEN
		SELECT * FROM clients;
	ELSE
		SELECT *
		FROM clients c
		WHERE c.state = state;
    	END IF;
END$$
DELIMITER ;
```

What if we want to return the entire clients if the state parameter is NULL. Instead of giving the parameter a default value we can return the entire clients using a SELECT statement. Then we add an ELSE statement when the first condition is not true then we want to return the client in given state.  
This code can be further simplified.

```sql
DELIMITER $$
CREATE PROCEDURE get_clients_by_state(state CHAR(2))
BEGIN
	SELECT *
	FROM clients c
	WHERE c.state = IFNULL(state, c.state);
END$$
DELIMITER ; 
```
Here we use the IFNULL function and pass the state parameter as the first argument and c.state as the second argument.  
As we learned earlier the IFNULL function returns the value of the second argument if the value of the first argument is NULL.

```sql
DELIMITER $$
CREATE PROCEDURE get_payments(client_id INT(11), payment_method_id TINYINT(4))
BEGIN
	SELECT * FROM payments p
    	WHERE 
		p.client_id = IFNULL(client_id, p.client_id) 
		AND 
		p.payment_method = IFNULL(payment_method_id, p.payment_method);
END$$
DELIMITER ;
```

Here we are creating a stored procedure with two parameters and providing default values for both if they are NULL when this stored procedure is called.

## Parameter validation

We can also use stored_procedure to insert, update and delete data.

```sql
CREATE PROCEDURE `make_payment`(invoice_id INT, payment_amount DECIMAL(9,2), payment_date DATE)
BEGIN
	IF payment_amount <= 0 THEN
    	SIGNAL SQLSTATE "22003"
		SET MESSAGE_TEXT = "Invalid payment amount";
	END IF;
	UPDATE invoices i
    	SET 
		i.payment_total = payment_amount,
		i.payment_date = payment_date
	WHERE 
		i.invoice_id = invoice_id;
END
```

Here we use parameter validation to ensure our procedure doesn't accidentally store bad data in our database.  
The procedure that we created here update the payment details of a invoice with given id. It takes three parameters and in the IF statement we are checking whether the payment amount is a valid amount if the condition evaluates to true MYSQL will signal an error to the user.  
The SIGNAL statement raises an error. 

```sql
SIGNAL SQLSTATE "22003"
		SET MESSAGE_TEXT = "Invalid payment amount";
```

We use SIGNAL keyword followed by a state keyword and a string literal that contains the error code.
The first two letter represents the class of the error code. Most of the times in stored procedure we deal with out of range exception whose code is 22003.  
Note that error code is string. Here we can set an optional error message so that the caller of this procedure will know why something failed.  
It is a good practice to set a descriptive error message.  
[Here we can find the complete list of error codes.](https://www.ibm.com/support/knowledgecenter/SSEPEK_11.0.0/codes/src/tpc/db2z_sqlstatevalues.html)

Too much good thing is a bad thing. If we write too much of a validation logic here our stored procedure will end up becoming very bloated and very hard to maintain.  
So keep your validation logic to bare minimum. We should do a more comprehensive validation in our application.

## Output Parameters

We can also use parameters to return values to the calling program.

```sql
CREATE PROCEDURE `get_unpaid_invoices_for_client`(client_id INT)
BEGIN
	SELECT COUNT(*), SUM(invoice_total) 
	FROM invoices i
    	WHERE i.client_id = client_id AND payment_total = 0;
END
```

When calling this stored procedure it returns the count and sum of all unpaid invocie total for a given client. We pass client_id as a parameter to our stored procedure function. We can also recieve the returned values through parameters.

```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `get_unpaid_invoices_for_client`(
	client_id INT, 
    	OUT invoices_count INT, 
    	OUT invoices_total DECIMAL(9,2)
)
BEGIN
	SELECT COUNT(*), SUM(invoice_total)
    	INTO invoices_count, invoices_total
    	FROM invoices i
    	WHERE i.client_id = client_id AND payment_total = 0;
END
```

We need to add couple more parameters here invoice_count and invoice_total of type INT and DECIMAL(9,2) respectively.  
By default all these parameters in the stored procedures are input parameters which means we can use them only to pass values to our procedures.  
So here we need to prefix the parameters with OUT keyword and this marks this parameters as output parameters. So we can use them to get values out of the procedure.  
In our SELECT statement we need to select the values INTO invoices_count, invoices_total. We are reading the values and copying them into the output parameters.

```sql
SET @invoices_count = 0;
SET @invoices_total = 0;
CALL sql_invoicing.get_unpaid_invoices_for_client(3, @invoices_count, @invoices_total);
SELECT @invoices_count, @invoices_total;
```
This is the code that we need to write to call the procedure with output parameters.  
In this case first we need to define two variables invoices_count and invoices_total, these are what we call a user defined variables.  
A variable is basically a object which we can use to store a value in memory. To define a variable we need to prefix it with @ sign.  
Here using the SET statement we are defining two variables and initializing them to 0.  
And when calling the procedure we need to pass these variables. The first arg is the client_id and the other arguments here are the variables that we defined earlier.
After calling the procedure we need to read the variables using a SELECT statement which will return the results.

## Variables

```sql
SET @invoices_count = 0
```
We define a variable using the SET statement and prefix them using a @ sign. Quite often we these variables when we call stored procedures that have output parameters.  
We pass these variables to get the value of the output parameter. These variables will be in the memory during the entire client's session.  
Once the client disconnect from the server these varaibles are freed out. So we refer to these as User or session variables.  

In MySQL we also have another type of variable called local variable and these are the variables that we define inside of the stored procedures or a function.  
These local variables don't stay in memory for the entire user's session. As soon as our stored procedure finish execution these variables are freed out.  Quite often we use these type of variables to perform calculation in stored procedure.

```sql
CREATE PROCEDURE `get_risk_factor`()
BEGIN
	DECLARE risk_factor DECIMAL(9,2) DEFAULT 0;
    	DECLARE invoices_total DECIMAL(9,2);
	DECLARE invoices_count INT;
    
    	SELECT COUNT(*), SUM(invoice_total)
    	INTO invoices_count, invoices_total
    	FROM invoices;
    
    	SET risk_factor = invoices_total / invoices_count * 5;
    
    	SELECT risk_factor;
END
```

To calculate the risk factor first we need to declare variables on the top right after the BEGIN statement. We use DECLARE statement to declare a variable followed by the name. 
Next we need to specify the data type. We can optionally give our variable a default value otherwise it is gonna be null.  
We declare two more variables invoices_total and invoices_count. Using the SELECT statement we calculate the count and invoice total and right after the INTO statement we give our two variables which corresponds to the value that we selected before.
We use the SET statement to set the value of the variable. Finally SELECT the risk_factor variable after the calculation.
As soon as the stored procedure finish executing the variable we declared inside will go out of memory.

## Functions

In MySQL we can create our own functions. Functions are very similar to stored procedures but the main difference is that the function can only return a single value.  
So unlike stored procedures they cannot return a result sets with multiple rows and columns.

```sql
CREATE FUNCTION `get_risk_factor_for_client`(client_id INT) 
RETURNS INT(11)
READS SQL DATA
BEGIN
	DECLARE risk_factor DECIMAL(9, 2) DEFAULT 0;
    	DECLARE invoices_total DECIMAL(9, 2);
    	DECLARE invoices_count INT;
    
    	SELECT COUNT(*), SUM(i.invoice_total)
    	INTO invoices_count, invoices_total
    	FROM invoices i
    	WHERE i.client_id = client_id;
    
    	SET risk_factor = invoices_total / invoices_count * 5;
    
RETURN IFNULL(risk_factor, 0);
END
```
This function returns the risk factor per client. The syntax for creating functions are very similar to the syntax for creating the stored procedures.  
The RETURNS statement is one of the main differences between the functions and stored procedures which specifies the type of value this function returns.  
Right after the return statement we need to set the attributes of a function. Every mySQL function should have atleast one attribute.

* DETERMINISTIC - which means if we give this function the same set of values it always returns the same value. This is useful in situations when you are not gonna return the value based on the data in your database because the data can change. It always returns the same output for the same input.  

* READS SQL DATA -  this means that you are gonna have a SELECT statement in your function to read some data.

* MODIFIES SQL DATA - this means that you are gonna have a INSERT, DELETE, UPDATE statement in your function.  

We can have multiple attributes here. In our example the function is not DETERMINISTIC and also we are not gonna modify anything.  
Finally we should always return a value. Similar to the previous example for stored procedure but here we need to add a WHERE statement to find the risk factor per client.  
Also return the risk_factor varibale using IFNULL function. We can use this function in SELECT statement just like the built in functions in MySQL.  

```sql
DROP FUNCTION IF EXISTS get_risk_factor_for_client
```
This is how we DROP a function and preferabally we can type IF EXISTS keyword.

# Triggers

A trigger is a block of SQL code that automatically gets executed before or after an INSERT, UPDATE OR DELETE statement.  
Quite often we use triggers to enforce data consistency. In our sql_invoicing database we can have multiple payments towards our invoice.  
In the invoices table we have the payment_total column and the value of this column is equal to sum of all the payments made to that invoice.  
So whenever we insert a new record in the payments table we should make sure that payment_total in the invoices table gets updated.  
This is where we use a trigger.

## Creating a Trigger
```sql
DELIMITER $$
CREATE TRIGGER payments_after_insert
	AFTER INSERT ON payments
    	FOR EACH ROW
BEGIN
	UPDATE invoices
    	SET payment_total = payment_total + NEW.amount 
    	WHERE invoice_id = NEW.invoice_id;
END$$
DELIMITER ;
```
First we need to change the default delimiter then we use the CREATE TRIGGER statement then we give the trigger a name here payments_after_insert,  
this means that this payment is associated with the payments table and is fired after we insert a record. Next we have AFTER INSERT ON payments,  
here we could also use BEFORE and instead INSERT we could also use UPDATE OR DELETE depending on what we are trying to implement.  
But in this example we are using AFTER and INSERT. Next we need to type FOR EACH ROW and this means that this trigger will gets fired for each row that is get affected. 
We add BEGIN and END to indicate the body of the trigger. In the body of the trigger we can write any SQL code that modify our data for consistency.  
In this example we want to update the invoices table and increase the payment total amount.  
To get the new value i.e the new payment amount we use NEW keyword which returns the newly inserted row we also have OLD which is useful for updating or deleting the row, so the OLD keyword returns the OLD row or OLD values. Using the dot after NEW keyword we can access the individual attributes in this case amount. Next we add a WHERE clause.
In this trigger we can modify any table except the table that this trigger is for. Other wise we'll end up in infinite loop because this trigger will fire itself.  

```sql
INSERT INTO payments
VALUES(DEFAULT, 5, 3, "2020-12-01", 10, 1)
```
When executing this query the trigger will  kick in automatically after the query was executed. This will automatically update the payment_total column in invoices table.  

```sql
DROP TRIGGER IF EXISTS payments_after_delete;
DELIMITER $$
CREATE TRIGGER payments_after_delete
	AFTER DELETE ON payments
    	FOR EACH ROW
BEGIN
	UPDATE invoices
    	SET payment_total = payment_total - OLD.amount
	WHERE invoice_id = OLD.invoice_id;
END$$
DELIMITER ;
```
This trigger gets fired when we delete a payment.

## Viewing Triggers

```sql
SHOW TRIGGERS LIKE "..."
-- table_after_insert
```
We can use the show triggers statement to list the triggers that we created earlier.  
If we follow the convention for naming triggers we can use the LIKE operator to find the triggers associated with the given table.

## Dropping Triggers

```sql
DROP TRIGGER IF EXISTS payments_after_insert
```
Dropping triggers is very similar to dropping stored procedures. We use DROP TRIGGER statement and optionally and ideally we use IF EXISTS keyword followed by the name of the trigger.

## Using Triggers for auditing

Another common usecase for triggers is logging changes to the data for auditing.  
For example when someone inserts or deletes a record we can log that somewhere so that later we can come back and see who made what changes when.  
We create a new table payments_audit to log the data when changes happen.

```sql
DELIMITER $$

DROP TRIGGER IF EXISTS payments_after_insert;
CREATE TRIGGER payments_after_insert
	AFTER INSERT ON payments
    	FOR EACH ROW
BEGIN
	UPDATE invoices
    	SET payment_total = payment_total + NEW.amount
    	WHERE invoice_id = NEW.invoice_id;
    
    	INSERT INTO payments_audit
    	VALUES(NEW.client_id, NEW.date, NEW.amount, "Insert", NOW());
END$$
DELIMITER ;
```

Here in our trigger we use INSERT statement after the UPDATE statement for invoices table. This insert new row into payment_audit table for each change that takes place.  
We can make similar changes to the payments_after_delete. Just replace the NEW keyword with the OLD keyword.

```sql
DELIMITER $$

DROP TRIGGER IF EXISTS payments_after_delete;
CREATE TRIGGER payments_after_delete
	AFTER DELETE ON payments
    	FOR EACH ROW
BEGIN
	UPDATE invoices
    	SET payment_total = payment_total - OLD.amount
    	WHERE invoice_id = OLD.invoice_id;
    
    	INSERT INTO payments_audit
    	VALUES(OLD.client_id, OLD.date, OLD.amount, "Delete", NOW());
END$$
DELIMITER ;
```

So whenever we insert or delete a payment in the payments table this trigger will kick in and update the payment_total column in the invoices table and also inserts a new record in the payment_audit table. 

# Events

An Event is a task or a block of SQL code that gets executed according to a schedule. It can get executed once or like a regular basis like every day or once a month.  
So with events we can automate database maintainence tasks like deleting stale data or copying data from one table to an archived table or aggregating data for generating reports.  

## Creating a Event
Before we create a event we need to turn on MySQL event_scheduler. That's basically a process that runs in the background and it constantly looks for events to execute.

```sql
SHOW VARIABLES LIKE "event%";
SET GLOBAL event_scheduler = ON
```
This command returns all the system varibles in the MySQL. Using LIKE operator we can retrieve only the event_scheduler variable.  
Using the SET GLOBAL statement we can turn on the event_scheduler if it is turned OFF. If you don't want events you can turn this off to save system resources.  

```sql
DELIMITER $$

CREATE EVENT yearly_delete_stale_audit_data
	ON SCHEDULE
    	EVERY 1 YEAR STARTS "2020-01-01" ENDS "2030-01-01"
DO BEGIN
	DELETE FROM payment_audit
    	WHERE action_date < NOW() - INTERVAL 1 YEAR;
END$$

DELIMITER ;
```

To create a event we use CREATE EVENT statement followed by the name of the event. By convention always start the event names with the intervals based on the event execution.  
After the event name we type ON SCHEDULE and here we are gonna provide the schedule for this event, how often we are gonna execute this tasks once or on a regular basis.  
If you want to execute only once here we use AT keyword followed by the date time value.  
Or if you want to execute this on a regular basis instead of the AT keyoword we use EVERY keyword with the interval depending on what we implement.  
Here we can optionally give start and end time. Then we type DO followed by BEGIN and END. In the body of the event we can write our SQL code.  
In this example we are deleting all the audit records that are older than one year.

## Viewing, Dropping and Altering Events

```sql
SHOW EVENTS LIKE "..."
```
To view the events in the current database we use SHOW EVENTS statements.   
As I mentioned earlier it is good practice to start the event name with their interval like hourly, monthly, yearly or once for one time events.  
With this convention we can easily filter events using the LIKE operator.

```sql
DROP EVENTS IF EXISTS yearly_delete_stale_audit_data
```

To drop a event we use DROP EVENT statement followed by the name of the event.

```sql
DELIMITER $$

ALTER EVENT yearly_delete_stale_audit_data
	ON SCHEDULE
    	EVERY 1 YEAR STARTS "2020-01-01" ENDS "2030-01-01"
DO BEGIN
	DELETE FROM payment_audit
    	WHERE action_date < NOW() - INTERVAL 1 YEAR;
END$$

DELIMITER ;
```
We also have ALTER EVENT statment to make any changes to the event without any need to drop and recreate it. The syntac is exacly same as the create event statement.  
Insted of the CREATE statement we use ALTER statement.  

```sql
ALTER EVENT yearly_delete_stale_audit_data ENABLE;
```

But we also use ALTER EVENT statement to temporarily ENABLE or DISABLE an event.

# Transactions

A Transaction is a group of SQL statements that represent a single unit of work. All these statements should be completed succesfully or the transactions will fail.  
If the first operation succeeds and the second operation fails we need to roll back or revert the changes by the first operation.  
We use transactions in situations where we want to make multiple changes to the database and we want all these changes to succedd or fail together as a single unit.  

These transactions have few properties that we need to know. We refer to these properties as **ACID**.  

* Atomicity - This means our transactions are like atoms. They are unbreakable. Each transaction is a single unit of work no matter how many statements it contains. Either all these statements gets executed successfully and the transaction is committed or the transaction is rolled back and all the changes are undone.  

* Consistency - This means that our database will always remain in a consistent state.  

* Isolation - That means these transactions are isolated or protected from each other if they try to modify the same data. So they cannot interfere with each other. If multiple transactions try to update the same data the rows that are being affected get locked so only transaction at a time can update those rows. Other transactions have to wait for that transaction to complete.  

* Durability - That means once a transaction is committed, the cahnges made by the transactions are permanent. So if you have a power failure or a system crash you are not gonna lose the changes.  

## Creating Transactions

```sql
USE sql_store;

START TRANSACTION;

INSERT INTO orders(cusomer_id, order_date, status)
VALUES(1, "2020-12-01", 1);

INSERT INTO order_items
VALUES(LAST_INSERT_ID(), 1, 1, 10);

COMMIT;
```

To create a transaction we use START TRANSACTION statement. Here in this transaction we are inserting a order into the orders table. Next we are inserting the order items.  
For the order_id I am using the LAST_INSERT_ID() function which returns the id of the newly inserted order. Finally we need to close this transaction with the commit statement. 
When MySQL sees this COMMIT command it will write all the changes to the database.  
If one of the changes fails it automatically undo the previous changes and we say the transaction is rolled back.  
Most of the time this is how we code a transaction we have START TRANSACTION statement on the top and a COMMIT statement down the bottom. 
But there maybe situation when we need to do error checking and manually roll back a transaction.  
In those situation instead of the COMMIT statement we use the ROLLBACK statement. This will rollback the situation and undo all the changes.

MySQL wraps every single statement that we write inside a transaction and it will do a commit if that statement didn't return a error.  
So whenever we have an INSERT, UPDATE or a DELETE statement MySQL wraps this inside a transaction and then it'll do a commit automatically.  

```sql
SHOW VARIABLES LIKE "autocommit"
```
This is controlled by a system variable called auto commit. So whenever we execute a single statement MySQL puts that statement in a transaction and commits it if the statement doesn't raise an error.

## Concurrency and Locking

So far we've been the only user of the database but in real it quite possible that two or more users will try to access the same data at the same time.  
This is what we call concurrency. Concurrency can become a problem when one user modifies the data that other users are trying to retrieve or modify.

```sql
USE sql_store;

START TRANSACTION;

UPDATE customers
SET points = points + 10
WHERE customer_id = 1;

COMMIT;
```

We are gonna simulate two customers trying to update the points for a given customer. To make this work create two separate sessions in MySQL workbench.  
Copy the code and paste it in new session. Now execute the code line by line but don't commit yet.  
In the second session you can see a spinner which means the update is running because when we executed the first update MySQL will put a lock on the customer row that we updated.  
So if another transaction tries to update the same row it has to wait untill the first transaction is complete either committed or rolled back that is why we had a spinner.  
So if a transaction tries to modify a row or multiple rows it puts lock on these rows and this lock prevents other transaction from modiying these rows untill the first transaction is done either it's commited or rolled back.

## Concurrency Problems

Let's look at the common problems concurrency brings.

* Lost Updates   
This happens when two transactions try to update the same data and we don't use locks. In this situation the transactions that commits later will override the changes made by the previous transaction. To prevent this from happening we use locks. By default MySQL uses a locking mecahnism to prevent two transactions from updating the same data at the same time. They will run in sequence one after another and we'll have both updates. 

* Dirty Reads  
A dirty read happens when a transaction reads a data that hasn't been committed yet. To solve this problem we need to provide a level of isolation around our transactions. So that a data modified by a transaction is not immediately visible to the other transactions unless it's committed. The standard SQL defines four transaction isolation levels. One of these is *READ COMMITTED*. When we use this isolation level for a transaction that transaction can only read committed data, with this we don't have dirty reads. So what if our data gets changed after our transaction completes, it doesn't really matter. What matters is that any data that we read is committed at the moment it is read. So when we set the isolation level of a transaction to *READ COMMITED* that transaction will only read committed data.

* Non-repeating Reads  
By adding more isolation to our transactions we can guarantee that our transaction can only read committed data. But what if during the course of the transaction we read something twice and get different results. The SQL standard defines another isolation level called *REPEATABLE READ*, with this level our reads are repeatable and consistent even if the data gets changed by the other transactions. We'll see the snapshot that was established by the first read.

* Phantom Reads  
Phantom means ghost. So we have data that suddenly appears like a ghost and we missed them in the query because they get added, updated or removed after we execute our query. To solve this problem we have another isolation level called *SERIALIZABLE* and this will guarantee that our transactions will be aware of changes currently being made by other transactions to the data. If there are other transactions modifying the data that can impact our query results, our transactions has to wait for them to complete. So the transaction will be executed sequentially. This is the highest level of isolation that we can apply to our transaction and it gives us the most certainity in our operations but it comes with a cost. The more users and concurrent transactions we have the more waits we are gonna experience and our system is gonna slow down. So this isolation level can affect performance and scalability. For this reason we should reserve this only in scenerios where it's absolutely critical and necessary to prevent phantom reads.

## Transaction Isolation Levels

Let's review the isolation level one more time and also summarize everything in a way that we can easily remember.

![Transaction Isolation Levels](https://github.com/guru-apr21/Learn-SQL/blob/main/5-%20Transaction%20Isolation%20Levels.mp4%20-%20VLC%20media%20player%2016-12-2020%2014_50_10.png)  

*READ UNCOMMITED* doesn't really protect us from any of this problems because our transactions are not isolated from each other and they can read uncommited changes by each other.  
*SERIALIZABLE* puts overhead on the server because it needs extra resources in terms of memory and CPU to manage the transactions that have to wait.  
So the more we increase the isolation level the more performance and scalability problems we are gonna experience because more locks will be invloved to isolate the transactions. So to recap a lower isolation level gives us more concurrency so more users can access the same data at the same time but more concurrency means more concurrency problems. On the flip side we achieve a better performance because we need fewer locks to isolate transactions from each other.  
A higer isolation level restreak concurrency and that means fewer concurrency problems but at the cost of decreased performance and scalability because we need more locks and resources.  
The fastest isolation level is read uncommitted because it doesn't set any locks and it ignores the locks set by other transactions for this reason we may experience all concurrency problems. As we go down this list we get better protection from concurrency problems but that also means we are gonna use more locks and this requires more resorces which can hurt performance and scalability.  
In MySQL the default transaction isolation level is *REPEATABLE READ* which work well in most scenerios. It is faster than *SERIALIZABLE* and prevents most concurrency problems 
except phantom reads.

```sql
SHOW VARIABLES LIKE "transaction_isolation";
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

This query returns the current isolation level which is *REPEATABLE READ* this is the default in MySQL.  
To change the transaction isolation level we use the SET TRANSACTION ISOLATION LEVEL statement followed by the name of the new isolation level. This will set the isolation level for next transaction.  

```sql
SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

We can also set the isolation level for all future transaction in the current session or connection, so we add the SESSION keyword next to the SET keyword. So as long as we have the session or connection open all the future transactions will have the given isolation level.  

```sql
SET GLOBAL TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

We can also set the isolation level globally for all new transactions in all sessions for that we use the GLOBAL keyword right after the SET keyword. 
 
## READ UNCOMMITTED Isolation Level

```sql
USE sql_store;
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
SELECT points FROM customers WHERE customer_id = 1
```

Open two new connections to simulate two new clients. In the first session set the transaction isolation level to *READ UNCOMMITTED*. So with this isolation level we are gonna read uncommitted data i.e, we are gonna have dirty reads. Then select the points for customer with id 1.  

```sql
USE sql_store;
START TRANSACTION;
UPDATE customers
SET points = 2293
WHERE customer_id = 1;
COMMIT;
```

In the second session start a transaction and update the points of the customer with the id 1 and then finally commit.  
In the first session execute only the SET transaction statement so the next transaction is gonna have *READ UNCOMMITTED* isolation level.  
Now in the second session execute all the statements line by line except commit. Now execute the SELECT statemet in the first session.  
This returns the uncommitted data because we set the isolation level to *READ UNCOMMITTED*. 
*READ UNCOMMITTED* is the lowest isolation level and with this isolation level we may experience all concurrency problems.

## READ COMMITTED Isolation level

```sql
USE sql_store;
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
SELECT points FROM customers WHERE customer_id = 1
```
```sql
USE sql_store;
START TRANSACTION;
UPDATE customers
SET points = 20
WHERE customer_id = 1;
COMMIT;
```
Here we set the transaction isolation level to *READ COMMITTED* which returns only the committed data. Unlike the *READ UNCOMMITTED* it doesn't return the uncommitted data.  
At this isolation level we don't have any dirty reads but we have another problem. We have unrepeatable reads.  
It is possible that during a transaction we read something twice but get different values each time.

```sql
USE sql_store;
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
START TRANSACTION;
SELECT points FROM customers WHERE customer_id = 1;
SELECT points FROM customers WHERE customer_id = 1;
COMMIT;
```

Here we start a transaction because we have two select statements. In the first session once again execute the line 2 to set the isolation level because this only applies to the next transaction. Otherwise it is gonna have the default isolation level which is *REPEATABLE READ*.  
Now start the transaction and read the points for first SELECT statement. Before executing the second SELECT statement go to the second session and update the points value.  
Now if we go back to the first session and read the points again we get different value. At this isolation level we have non repeatable or in-consistent reads.  
To solve this problem we need to increase the isolation level for this transaction.

## REPEATABLE READ Isolation level

With this Isolation level our reads are gonna be consistent and repeatable.

```sql
USE sql_store;
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
START TRANSACTION;
SELECT points FROM customers WHERE customer_id = 1;
SELECT points FROM customers WHERE customer_id = 1;
COMMIT;
```
Just change the isolation level to *REPEATABLE READ*.Now let's execute the line 2 and then start a new transaction after that execute the SELECT statement and read the value.  
Now before executing the second SELECT statement go back to the second session and update the points to some other value.  
Now bact to the first session when we execute the second SELECT statement we are gonna see the same value. So our reads are repeatable and consistent.  
So this is benefit of this isolation level. As I mentioned earlier this is the default isolation level of the MySQL that solves most of the concurrency problems.  
But at this level we have one problem that is phantom reads.

```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
START TRANSACTION;
SELECT * FROM customers WHERE state = "VA";
COMMIT;
```

```sql
START TRANSACTION;
UPDATE customers
SET state = "VA"
WHERE customer_id = 1;
COMMIT;
```

So the scenerio that we are simulating here is that one client tries to read the customers that are located in Virginia and at the same time another client is updating the data such that the customer with id 1 should be included in the query that client no 1 is executing.  
So even if we change the state of the customer to Virginia, the SELECT statement do not return the customer because with the repeatable reads our reads are going to be consistent. This is what we call a phantom read.

SERIALIZABLE Isolation Level

This isolation level provides the highest level of isolation and solves all concurrency problems. At this level our transactions are executed in sequence one after another.  
We really don't have concurrency, the experience we get is like single user system.  
One user executing different commands against the database, these commands are executed sequentially.

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
START TRANSACTION;
SELECT * FROM customers WHERE state = "VA";
COMMIT;
```

```sql
START TRANSACTION;
UPDATE customers
SET state = "VA"
WHERE customer_id = 1;
COMMIT;
```
Change the transaction isolation level to *SERIALIZABLE*.  
While the first client is trying to read the customers in Virginia, another client is updating the customer with id 3.  
So this customer should be included in our query otherwise we are gonna have phantom reads. The transaction will wait for the transaction to finish.  
This is the result of SERIALIZABLE isolation level. So with the SERIALIZABLE isolation level we can solve all concurrency problems because our transactions are executed sequentially. The more users and more concurrent requests we have the more waits we are gonna experience.  
So as I mentioned earlier use this isolation level only in the scenerios where you want to prevent the phantom reads.

## Deadlocks

A deadlock happens when different transactions cannot complete because each transaction holds a lock that other needs. So both transaction keeps waiting for each other and never release their lock.

```sql
USE sql_store;
START TRANSACTION;
UPDATE customers SET state = "VA" WHERE customer_id = 1;
UPDATE orders SET status = 1 WHERE order_id = 1;
COMMIT;
```
```sql
USE sql_store;
START TRANSACTION;
UPDATE orders SET status = 1 WHERE order_id = 1;
UPDATE customers SET state = "VA" WHERE customer_id = 1;
COMMIT;
```
Here in the first session we are updating one record in the customers table and one record in the orders table.
In the second session change the order of the update statements.  
When we execute the transactions in both the sessions simultaneously both these transactions will wait for each other and they will never be able to complete.  
This is what we call a deadlock. In this situation MySQL treats one of the transactions as a victim and rolls it back.  
We can never completely get rid of them but minimize their likelihood.

# Data Types

It is really important to understand when to use which data types. In MySQL we have several categories of data types.

* String Types
* Numeric Types
* Date and Time Types
* Blob Types - for storing binary data
* Spatial Types - for storing geometric and geographical values
