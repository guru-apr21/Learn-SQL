# Learn-SQL
###-Shorthand SQL commands
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
