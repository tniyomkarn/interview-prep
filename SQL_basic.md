Basic SQL
=========

**SQL** (Structured Query Language) is a programming language designed for managing data in a relational database allowing its users to read, manipulate, and change data.
Benefits:
- It’s semantically easy to understand and learn.
- It can be used to access large amounts of data directly where it’s stored (no need to copy data into other applications)
- Compared to spreadsheet tools, data analysis done in SQL is easy to audit and replicate.

**Database**: an organized collection of data.

**Relational Database**:  is a type of database that stores and provides access to data points that are related to one another. Relational databases are based on the relational model, an intuitive, straightforward way of representing data in tables. In a relational database, each row in the table is a record with a unique ID called the key. The columns of the table hold attributes of the data, and each record usually has a value for each attribute, making it easy to establish the relationships among data points.

**Database Schema**: defines how the data is organized and how the relations among them are associated. It formulates all the constraints that are to be applied on the data.
- Physical Database Schema − This schema pertains to the actual storage of data and its form of storage like files, indices, etc. It defines how the data will be stored in a secondary storage.
- Logical Database Schema − This schema defines all the logical constraints that need to be applied on the data stored. It defines tables, views, and integrity constraints.

SQL SELECT
----------
There are two required ingredients in any SQL query: `SELECT` and `FROM` and they have to be in that order. 
- SELECT indicates which columns you’d like to view.
- FROM identifies the table that they live in.

```sql
SELECT year,
       month,
       west
  FROM tutorial.us_housing_units
```
To select all columns, use `SELECT *`

Upper/lowercase does not matter for commands. Neither do tabs, space, and newline.

Use AS to rename columns for presentation.
```sql
SELECT west AS "West Region"
  FROM tutorial.us_housing_units
  ```
  
  ```sql
  SELECT west AS West_Region,
         south AS South_Region
    FROM tutorial.us_housing_units
  ```
  
  Note that new column names work with or without quotes, however **a name with space NEEDS quotes**.
  
SQL LIMIT
---------
 Use limit too restrict the number of returned rows to prevent queries taking too long.
  ```sql
  SELECT *
    FROM tutorial.us_housing_units
  LIMIT 100
 ```
   
SQL WHERE
---------
Use `WHERE` clause to filter data.

```sql
SELECT *
  FROM tutorial.us_housing_units
 WHERE month = 1
 ```
 Note: the clauses always need to be in this order: `SELECT`, `FROM`, `WHERE`.
 
 `WHERE` clause must contain comparison or logical operators.
 
 SQL Comparison Operators
 ------------------------
 ### Comparison operators on numerical data
 `=` `<>` `!=` `>` `<` `>=` `<=`
 ```sql
 SELECT *
  FROM tutorial.us_housing_units
 WHERE west > 30
 ```
 ### Comparison operators on non-numerical data
 
Use the same operators as numerical. Values are compared in alphabetical order.

e.g 'J' < 'Ja'

### Arithmetic in SQL

 We can perform artihmatic calculation for each row of data using `+`, `-`, `*`, `/`. *Note that if you want to add values across multiple rows, you’ll need to use `aggregate` functions*
 ```sql
 SELECT year,
       month,
       west,
       south,
       west + south - 4 * year AS nonsense_column
  FROM tutorial.us_housing_units
  ```
  ```sql
  SELECT year,
       month,
       west/(west + south + midwest + northeast)*100 AS west_pct,
       south/(west + south + midwest + northeast)*100 AS south_pct,
       midwest/(west + south + midwest + northeast)*100 AS midwest_pct,
       northeast/(west + south + midwest + northeast)*100 AS northeast_pct
  FROM tutorial.us_housing_units
 WHERE year >= 2000
  ```
  ```sql
  SELECT year,
       month,
       west/total*100 AS west_pct,
       south/total*100 AS south_pct,
       midwest/total*100 AS midwest_pct,
       northeast/total*100 AS northeast_pct
  FROM (
  SELECT *, (west + south + midwest + northeast) AS total
      FROM tutorial.us_housing_units
  ) AS temp_table
 WHERE year >= 2000
 ```
- You can do `SELECT *, a+b AS c` !
- You NEED `AS` for subquery at `FROM`. You must do `FROM (#subquery#) AS new_table` but NOT `FROM (#subquery#)`

SQL Logical Operators
---------------------

- `LIKE` allows you to match similar values, instead of exact values.
- `IN` allows you to specify a list of values you’d like to include.
- `BETWEEN` allows you to select only rows within a certain range.
- `IS` NULL allows you to select rows that contain no data in a given column.
- `AND` allows you to select only rows that satisfy two conditions.
- `OR` allows you to select rows that satisfy either of two conditions.
- `NOT` allows you to select rows that do not match a certain condition.

SQL LIKE
--------

 `LIKE` allows you to match similar values. `ILIKE` is the same but it ignores case, They usually come with 
 - `%`wildcard  represents any **character** or **set of characters**:
 ```sql
 SELECT *
  FROM tutorial.billboard_top_100_year_end
 WHERE "group" LIKE 'Snoop%'
 ```
 *Note that the columna named "group" is the same as function name, so we use double quotes to specify that we are refering to a column name.*
 
 - `_`placholder to substitute for an **individual character**:
 ```sql
 SELECT *
  FROM tutorial.billboard_top_100_year_end
 WHERE artist ILIKE 'dr_ke'
 ```
 * `LIKE` cannot be used with numerical types
 
 SQL IN
 ------
 `IN` allows you to specify a list of values you’d like to include. It can be used with both numeric/ non-numeric values. We use () for list in SQL.
 ```sql
 SELECT *
  FROM tutorial.billboard_top_100_year_end
 WHERE year_rank IN (1, 2, 3)
 ```
 ```sql
 SELECT *
  FROM tutorial.billboard_top_100_year_end
 WHERE artist IN ('Taylor Swift', 'Usher', 'Ludacris')
 ```
 
 SQL BETWEEN
 -----------
 always comes with `AND` and the range is ALL inclusive.
 ```sql
 SELECT *
  FROM tutorial.billboard_top_100_year_end
 WHERE year_rank BETWEEN 5 AND 10
 ```
 is equivalent to
 ```sql
 SELECT *
   FROM tutorial.billboard_top_100_year_end
  WHERE year_rank >= 5 AND year_rank <= 10
 ```
 * Between can be used with non-numeric values too, just like `>=` AND '<=`
 
 SQL IS NULL
 -----------
 `IS NULL` is used to check whether a particular row contains in that column.
 ```sql
 SELECT *
   FROM tutorial.billboard_top_100_year_end
  WHERE artist IS NULL
 ```
 - `WHERE artist = NULL` will not work—you can’t perform arithmetic on null values.
 - NULL + 1 + 2 = NULL
 - `SUM` will treat `NULL` as 0
 
 SQL AND
 -------
 `AND` is used to select only rows that satisfy two conditions that it joins. You can chain them together for multiple conditions.
 ```sql
 SELECT *
   FROM tutorial.billboard_top_100_year_end
  WHERE year = 2012
    AND year_rank <= 10
    AND "group" ILIKE '%feat%'

 ```
 
 SQL OR
 ------
 `OR` is used to select rows that satisfy either of two conditions or one of the multiple conditions if chained.
 ```sql
 SELECT *
   FROM tutorial.billboard_top_100_year_end
  WHERE song_name LIKE '%California%'
    AND (year BETWEEN 1970 AND 1979 OR year BETWEEN 1990 AND 1999)
 ````
 
 SQL NOT
 -------
 `NOT` is used to negate a condition: `NOT BETWEEN`, `NOT ILIKE`, `IS NOT NULL`
 
 You CANNOT do something like `NOT >` though.
 
 
 SQL ORDER BY
 ------------
 `ORDER BY` is used to reorder the query results in **ascending order** by default.
 To sort in descending order, add DESC after a column name.
 
 ```sql
 SELECT *
   FROM tutorial.billboard_top_100_year_end
  WHERE year = 2013
  ORDER BY year_rank DESC
 ```
 ### Ordering data by multiple columns
 
 Use comma to separate columns in `ORDER BY` to sort multiple columns from left to right.
 
 ```sql
 SELECT *
   FROM tutorial.billboard_top_100_year_end
   WHERE year_rank <= 3
  ORDER BY year DESC, year_rank
```

Commention in SQL
-----------------
Use -- or /* */
```sql
SELECT *  --This comment won't affect the way the code runs
  FROM tutorial.billboard_top_100_year_end

/* Here's a comment so long and descriptive that
it could only fit on multiple lines. Fortunately,
it, too, will not affect how this code runs. */
 ```
