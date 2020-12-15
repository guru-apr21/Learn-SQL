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
        	WHEN points BETWEEN 2000 AND 3000 THEN "Silver"
        	WHEN points < 2000 THEN "Bronze"
        	END AS category
FROM customers
ORDER BY points DESC
```
This query returns the customers and their category based on the points they have scored.
