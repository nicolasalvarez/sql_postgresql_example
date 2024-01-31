# sql_postgresql_example

This project has some SQL examples from https://www.postgresqltutorial.com/.

postgres and example databe setup: https://www.postgresqltutorial.com/postgresql-getting-started/install-postgresql-linux/

Example database: https://www.postgresqltutorial.com/wp-content/uploads/2019/05/dvdrental.zip


Show all databases in the PostgreSQL server:

    \l

Display all tables in the database:

    \dt


# QUERYING AND FILTERING DATA, SELECT

It is recommended to explicitly specify the column names in the SELECT clause whenever possible. This ensures that only the necessary data is retrieved from the database, contributing to more efficient and optimized queries.

    SELECT first_name, last_name, email FROM customer;
 
    SELECT * FROM customer;

## concatenation operator ||: 

    SELECT 
        first_name || ' ' || last_name AS "full name",
        email
    FROM 
        customer;

A column alias allows you to assign a column or an expression in the select list of a SELECT statement a temporary name. The column alias exists temporarily during the execution of the query.

    SELECT column_name AS alias_name
    FROM table_name;

## ORDER BY clause

    SELECT 
        select_list 
    FROM 
        table_name 
    ORDER BY 
        sort_expression1 [ASC | DESC] [NULLS FIRST | NULLS LAST], 
        sort_expression2 [ASC | DESC] [NULLS FIRST | NULLS LAST],
        ...;

Example:

    SELECT 
        first_name, 
        last_name,
        LENGTH(last_name) AS last_name_len 
    FROM 
        customer 
    ORDER BY 
        last_name_len DESC,
        first_name ASC;

## DISTINCT clause

The SELECT DISTINCT removes duplicate rows from a result set. The SELECT DISTINCT clause retains one row for each group of duplicates.

The SELECT DISTINCT clause can be applied to one or more columns in the select list of the SELECT statement.

    SELECT
        DISTINCT column1, column2
    FROM
        table_name;

## WHERE clause

To retrieve rows that satisfy a specified condition, you use a WHERE clause.

    SELECT 
        select_list 
    FROM 
        table_name 
    WHERE 
        condition # AND, OR operators
    ORDER BY 
        sort_expression;

## LIMIT & FETCH clauses

PostgreSQL LIMIT is an optional clause of the SELECT statement that constrains the number of rows returned by the query.

If you want to skip a number of rows before returning the row_count rows, you can use OFFSET clause placed after the LIMIT.

    SELECT 
        select_list 
    FROM 
        table_name 
    ORDER BY 
        sort_expression 
    LIMIT 
        row_count OFFSET row_to_skip;

The LIMIT clause is not a SQL standard. To conform with the SQL standard, PostgreSQL supports the FETCH clause to skip a certain number of rows and then fetch a specific number of rows.

    OFFSET row_to_skip { ROW | ROWS }
    FETCH { FIRST | NEXT } [ row_count ] { ROW | ROWS } ONLY

# JOIN TABLES

## Setting up sample tables

    CREATE TABLE basket_a (
        a INT PRIMARY KEY,
        fruit_a VARCHAR (100) NOT NULL
    );

    CREATE TABLE basket_b (
        b INT PRIMARY KEY,
        fruit_b VARCHAR (100) NOT NULL
    );

    INSERT INTO basket_a (a, fruit_a)
    VALUES
        (1, 'Apple'),
        (2, 'Orange'),
        (3, 'Banana'),
        (4, 'Cucumber');

    INSERT INTO basket_b (b, fruit_b)
    VALUES
        (1, 'Orange'),
        (2, 'Apple'),
        (3, 'Watermelon'),
        (4, 'Pear');

## INNER JOIN

The inner join examines each row in the first table (basket_a). It compares the value in the fruit_a column with the value in the fruit_b column of each row in the second table (basket_b). If these values are equal, the inner join creates a new row that contains columns from both tables and adds this new row to the result set.

    SELECT
        a,
        fruit_a,
        b,
        fruit_b
    FROM
        basket_a
    INNER JOIN basket_b
        ON fruit_a = fruit_b;

Output:

    a | fruit_a | b | fruit_b
    ---+---------+---+---------
    1 | Apple   | 2 | Apple
    2 | Orange  | 1 | Orange
    (2 rows)

## LEFT JOIN

The following statement uses the left join clause to join the basket_a table with the basket_b table. In the left join context, the first table is called the left table and the second table is called the right table.

    SELECT
        a,
        fruit_a,
        b,
        fruit_b
    FROM
        basket_a
    LEFT JOIN basket_b 
    ON fruit_a = fruit_b;

Output:

    a | fruit_a  |  b   | fruit_b
    ---+----------+------+---------
    1 | Apple    |    2 | Apple
    2 | Orange   |    1 | Orange
    3 | Banana   | null | null
    4 | Cucumber | null | null
    (4 rows)

The left join starts selecting data from the left table. It compares values in the fruit_a column with the values in the fruit_b column in the basket_b table.

If these values are equal, the left join creates a new row that contains columns of both tables and adds this new row to the result set. (see the row #1 and #2 in the result set).

In case the values do not equal, the left join also creates a new row that contains columns from both tables and adds it to the result set. However, it fills the columns of the right table (basket_b) with null. (see the row #3 and #4 in the result set).

## LEFT OUTER JOIN

To select rows from the left table that do not have matching rows in the right table, you use the left join with a WHERE clause.

    SELECT
        a,
        fruit_a,
        b,
        fruit_b
    FROM
        basket_a
    LEFT JOIN basket_b 
        ON fruit_a = fruit_b
    WHERE b IS NULL;

Output:

    a | fruit_a  |  b   | fruit_b
    ---+----------+------+---------
    3 | Banana   | null | null
    4 | Cucumber | null | null
    (2 rows)

## RIGHT JOIN

The right join is a reversed version of the left join.

    SELECT
        a,
        fruit_a,
        b,
        fruit_b
    FROM
        basket_a
    RIGHT JOIN basket_b ON fruit_a = fruit_b;

Output:

    a   | fruit_a | b |  fruit_b
    ------+---------+---+------------
        2 | Orange  | 1 | Orange
        1 | Apple   | 2 | Apple
     null | null    | 3 | Watermelon
     null | null    | 4 | Pear
    (4 rows)

## RIGHT OUTER JOIN

Similarly, you can get rows from the right table that do not have matching rows from the left table by adding a WHERE clause as follows:

    SELECT
        a,
        fruit_a,
        b,
        fruit_b
    FROM
        basket_a
    RIGHT JOIN basket_b 
    ON fruit_a = fruit_b
    WHERE a IS NULL;

Output:

    a   | fruit_a | b |  fruit_b
    ------+---------+---+------------
    null | null    | 3 | Watermelon
    null | null    | 4 | Pear
    (2 rows)

## FULL JOIN

The full outer join or full join returns a result set that contains all rows from both left and right tables, with the matching rows from both sides if available. In case there is no match, the columns of the table will be filled with NULL.

    SELECT
        a,
        fruit_a,
        b,
        fruit_b
    FROM
        basket_a
    FULL OUTER JOIN basket_b 
        ON fruit_a = fruit_b;

Output: 

      a   | fruit_a  |  b   |  fruit_b
    ------+----------+------+------------
        1 | Apple    |    2 | Apple
        2 | Orange   |    1 | Orange
        3 | Banana   | null | null
        4 | Cucumber | null | null
     null | null     |    3 | Watermelon
     null | null     |    4 | Pear
    (6 rows)

## FULL OUTER JOIN

To return rows in a table that do not have matching rows in the other, you use the full join with a WHERE clause like this:

    SELECT
        a,
        fruit_a,
        b,
        fruit_b
    FROM
        basket_a
    FULL JOIN basket_b 
    ON fruit_a = fruit_b
    WHERE a IS NULL OR b IS NULL;

Output: 

      a   | fruit_a  |  b   |  fruit_b
    ------+----------+------+------------
        3 | Banana   | null | null
        4 | Cucumber | null | null
     null | null     |    3 | Watermelon
     null | null     |    4 | Pear
    (4 rows)

## TABLE ALIASES

Typically, you use table aliases in a query that has a join clause to retrieve data from multiple related tables that share the same column name.

    SELECT 
        c.customer_id, 
        c.first_name, 
        p.amount, 
        p.payment_date 
    FROM 
        customer AS c 
    INNER JOIN payment AS p ON p.customer_id = c.customer_id 
    ORDER BY 
        p.payment_date DESC;

## SELF JOIN

A PostgreSQL self-join is a regular join that joins a table to itself using the INNER JOIN or LEFT JOIN.

Self-joins are very useful for querying hierarchical data or comparing rows within the same table.

    SELECT select_list
    FROM table_name t1
    INNER JOIN table_name t2 ON join_predicate;

## CROSS JOIN

A CROSS JOIN allows you to produce a cartesian product of rows in two tables. It means that the CROSS JOIN combines each row from the first table with every row from the second table, resulting in a complete combination of all rows.

If table1 has n rows and table2 has m rows, the CROSS JOIN will return a result set that has nxm rows.

    SELECT 
        select_list 
    FROM 
        table1 
    CROSS JOIN table2;

# GROUP DATA

## GROUP BY clause

The GROUP BY clause divides the rows returned from the SELECT statement into groups.

For each group, you can apply an aggregate function such as SUM() to calculate the sum of items or COUNT() to get the number of items in the groups.

The following illustrates the basic syntax of the GROUP BY clause:

    SELECT 
        column_1, 
        column_2,
        ...,
        aggregate_function(column_3)
    FROM 
        table_name
    GROUP BY 
        column_1,
        column_2,
        ...;

## HAVING clause

    SELECT 
        column1, 
        aggregate_function (column2) 
    FROM 
        table_name 
    GROUP BY 
        column1 
    HAVING 
        condition;

The WHERE clause filters the rows based on a specified condition whereas the HAVING clause filter groups of rows according to a specified condition.

In other words, you apply the condition in the WHERE clause to the rows while you apply the condition in the HAVING clause to the groups of rows.

# SET OPERATIONS

## UNION operator

The UNION operator allows you to combine the result sets of two or more SELECT statements into a single result set.

The following illustrates the syntax of the UNION operator:

    SELECT select_list
    FROM A
    UNION
    SELECT select_list
    FROM B;

In this syntax, the queries must conform to the following rules:

The number and the order of the columns in the select list of both queries must be the same.
* The data types of the columns in the select lists must be compatible.
* The UNION operator removes all duplicate rows from the combined data set. To retain the duplicate rows, you use the the UNION ALL instead.

Here’s the syntax of the UNION ALL operator:

    SELECT select_list
    FROM A
    UNION ALL
    SELECT select_list
    FROM B;

## INTERSECT operator

Like the UNION and EXCEPT operators, the PostgreSQL INTERSECT operator combines result sets of two SELECT statements into a single result set. The INTERSECT operator returns a result set containing rows that are available in both result sets.

Here is the basic syntax of the INTERSECT operator:

    SELECT select_list
    FROM A
    INTERSECT
    SELECT select_list
    FROM B;

To use the INTERSECT operator, the columns that appear in the SELECT statements must follow these rules:
* The number of columns and their order in queries must be the same.
* The data types of the columns in the queries must be compatible.

## EXCEPT operator

Like the UNION and INTERSECT operators, the EXCEPT operator returns rows by comparing the result sets of two or more queries.

The EXCEPT operator returns distinct rows from the first (left) query that are not in the second (right) query.

The following illustrates the syntax of the EXCEPT operator.

    SELECT select_list
    FROM A
    EXCEPT 
    SELECT select_list
    FROM B;

The queries that involve the EXCEPT need to follow these rules:

The number of columns and their orders must be the same in the two queries.
The data types of the respective columns must be compatible.

# SUBQUERY

## PostgreSQL subquery (aka inner query or nested query)

A subquery is a query nested within another query. A subquery is also known as an inner query or nested query.

A subquery can be useful for retrieving data that will be used by the main query as a condition for further data selection.

The basic syntax of the subquery is as follows:

    SELECT 
    select_list 
    FROM 
    table1 
    WHERE 
    columnA operator (
        SELECT 
        columnB 
        from 
        table2 
        WHERE 
        condition
    );

Example:

    SELECT 
        city 
    FROM 
        city 
    WHERE 
        country_id = (
            SELECT 
                country_id 
            FROM 
                country 
            WHERE 
                country = 'United States'
        ) 
    ORDER BY 
        city;

## ANY operator

The PostgreSQL ANY operator compares a value with a set of values returned by a subquery. It is commonly used in combination with comparison operators such as =, <, >, <=, >=, and <>.

Here’s the basic syntax of  the ANY operator:

    expresion operator ANY(subquery)

## ALL operator

The PostgreSQL ALL operator allows you to compare a value with all values in a set returned by a subquery.

Here’s the basic syntax of the ALL operator:

    expresion operator ALL(subquery)


## EXISTS operator

The EXISTS operator is a boolean operator that checks the existence of rows in a subquery.

Here’s the basic syntax of the EXISTS operator:

    SELECT 
        select_list 
    FROM 
        table1 
    WHERE 
        [NOT] EXISTS(
            SELECT 
                select_list 
            FROM 
                table2 
            WHERE 
                condition
    );

# COMMON TABLE EXPRESSION (CTE)

## COMMON TABLE EXPRESSION (CTE)

A common table expression (CTE) allows you to create a temporary result set within a query.

A CTE helps you enhance the readability of a complex query by breaking it down into smaller and more reusable parts

Here’s the basic syntax for creating a common table expression:

    WITH cte_name (column1, column2, ...) AS (
        -- CTE query
        SELECT ...
    )
    -- Main query using the CTE
    SELECT ...
    FROM cte_name;

Example:

    WITH cte_rental AS (
    SELECT 
        staff_id, 
        COUNT(rental_id) rental_count 
    FROM 
        rental 
    GROUP BY 
        staff_id
    ) 
    SELECT 
        s.staff_id, 
        first_name, 
        last_name, 
        rental_count 
    FROM 
        staff s 
        INNER JOIN cte_rental USING (staff_id);

The following are some advantages of using common table expressions or CTEs:

* Improve the readability of complex queries. You use CTEs to organize complex queries in a more organized and readable manner.
* Ability to create recursive queries, which are queries that reference themselves. The recursive queries come in handy when you want to query hierarchical data such as organization charts.
* Use in conjunction with window functions. You can use CTEs in conjunction with window functions to create an initial result set and use another select statement to further process this result set.

## Recursive Query

Use the WITH RECURSIVE syntax to define a recursive query.

Use a recursive query to deal with hierarchical or nested data structures such as trees or graphs.

# MODIFYING DATA

## INSERT statement

The PostgreSQL INSERT statement allows you to insert a new row into a table.

Here’s the basic syntax of the INSERT statement:

    INSERT INTO table1(column1, column2, …)
    VALUES (value1, value2, …);

## Inserting multiple rows into a table

To insert multiple rows into a table using a single INSERT statement, you use the following syntax:

    INSERT INTO table_name (column_list)
    VALUES
        (value_list_1),
        (value_list_2),
        ...
        (value_list_n);

## UPDATE statement

The PostgreSQL UPDATE statement allows you to update data in one or more columns of one or more rows in a table.

Here’s the basic syntax of the UPDATE statement:

    UPDATE table_name
        SET column1 = value1,
            column2 = value2,
            ...
    WHERE condition;

## UPDATE join syntax

Sometimes, you need to update data in a table based on values in another table. In this case, you can use the PostgreSQL UPDATE join.

Here’s the basic syntax of the UPDATE join statement:

    UPDATE table1
    SET table1.c1 = new_value
    FROM table2
    WHERE table1.c2 = table2.c2;

## DELETE statement

The PostgreSQL DELETE statement allows you to delete one or more rows from a table.

The following shows the basic syntax of the DELETE statement:

    DELETE FROM table_name
    WHERE condition;

## DELETE statement with USING clause

PostgreSQL does not support the DELETE JOIN statement like MySQL. Instead, it offers the USING clause in the DELETE statement that provides similar functionality to the DELETE JOIN.

Here’s the syntax of the DELETE USING statement:

    DELETE FROM table1
    USING table2
    WHERE condition
    RETURNING returning_columns;


## UPSERT statement

Upsert is a combination of update and insert. The upsert allows you to update an existing row or insert a new one if it doesn’t exist.

PostgreSQL does not have the UPSERT statement but it supports the upsert operation via the INSERT...ON CONFLICT statement.

Here’s the basic syntax of the INSERT...ON CONFLICT statement:

    INSERT INTO table_name (column1, column2, ...)
    VALUES (value1, value2, ...)
    ON CONFLICT (conflict_column)
    DO NOTHING | DO UPDATE SET column1 = value1, column2 = value2, ...;

# CONDITIONAL EXPRESSIONS & OPERATORS

## CASE

The PostgreSQL CASE expression is the same as IF/ELSE statement in other programming languages. It allows you to add if-else logic to the query to form a powerful query.

Since CASE is an expression, you can use it in any places where an expression can be used e.g.,SELECT, WHERE, GROUP BY, and HAVING clause.

The CASE expression has two forms: general and simple form.

### General CASE expression

    CASE 
        WHEN condition_1  THEN result_1
        WHEN condition_2  THEN result_2
        [WHEN ...]
        [ELSE else_result]
    END

### Simple CASE expression

    CASE expression
        WHEN value_1 THEN result_1
        WHEN value_2 THEN result_2 
        [WHEN ...]
    ELSE
        else_result
    END

## COALESCE

The COALESCE function accepts an unlimited number of arguments. It returns the first argument that is not null. If all arguments are null, the COALESCE function will return null.

    COALESCE (argument_1, argument_2, …);

## NULLIF

The NULLIF function returns a null value if argument_1 equals to argument_2, otherwise it returns argument_1.

    NULLIF(argument_1,argument_2);

## CAST operator

There are many cases that you want to convert a value of one data type into another. PostgreSQL provides you with the CAST operator that allows you to do this.

    CAST ( expression AS target_type );

In this syntax:

* First, specify an expression that can be a constant, a table column, an expression that evaluates to a value.
* Then, specify the target data type to which you want to convert the result of the expression.

Besides the type CAST syntax, you can use the following syntax to convert a value of one type into another:

    expression::type

# QUERY DEBUGGING / PERFORMANCE

## EXPLAIN statement

The EXPLAIN statement returns the execution plan which PostgreSQL planner generates for a given statement.

The EXPLAIN shows how tables involved in a statement will be scanned by index scan or sequential scan, etc., and if multiple tables are used, what kind of join algorithm will be used.

The most important and useful information that the EXPLAIN statement returns are start-cost before the first row can be returned and the total cost to return the complete result set.

The following shows the syntax of the EXPLAIN statement:

    EXPLAIN [ ( option [, ...] ) ] sql_statement;

where option can be one of the following:

    ANALYZE [ boolean ]
    VERBOSE [ boolean ]
    COSTS [ boolean ]
    BUFFERS [ boolean ]
    TIMING [ boolean ]  
    SUMMARY [ boolean ]
    FORMAT { TEXT | XML | JSON | YAML }

The boolean specifies whether the selected option should be turned on or off.

# POSTGRESQL INDEXES

## CREATE INDEX

An index is a separated data structure that speeds up the data retrieval on a table at the cost of additional writes and storage to maintain the index.

The following show the basic syntax of the CREATE INDEX statement:

    CREATE INDEX index_name ON table_name [USING method]
    (
        column_name [ASC | DESC] [NULLS {FIRST | LAST }],
        ...
    );

To check if a query uses an index or not, you use the EXPLAIN statement.

## DROP INDEX statement

    DROP INDEX  [ CONCURRENTLY]
    [ IF EXISTS ]  index_name 
    [ CASCADE | RESTRICT ];

## List Indexes using pg_indexes View

The pg_indexes view allows you to access useful information on each index in the PostgreSQL database. 

    SELECT
        tablename,
        indexname,
        indexdef
    FROM
        pg_indexes
    WHERE
        schemaname = 'public'
    ORDER BY
        tablename,
        indexname;

## List Indexes using psql command

If you use psql to connect to a PostgreSQL database and want to list all indexes of a table, you can use the \d psql command as follows:

    \d table_name

## Index Types

PostgreSQL has several index types: B-tree, Hash, GiST, SP-GiST, GIN, and BRIN. Each index type uses a different storage structure and algorithm to cope with different kinds of queries.

When you use the CREATE INDEX statement without specifying the index type, PostgreSQL uses the B-tree index type by default because it is best to fit the most common queries.

## UNIQUE index

The PostgreSQL UNIQUE index enforces the uniqueness of values in one or multiple columns. 

Only B-tree indexes can be declared as unique indexes.

When you define an UNIQUE index for a column, the column cannot store multiple rows with the same values.

    CREATE UNIQUE INDEX index_name
    ON table_name(column_name, [...]);

# POSTGRESQL VIEWS

## PostgreSQL Views

A view is a named query stored in the PostgreSQL database server. A view is defined based on one or more tables which are known as base tables, and the query that defines the view is referred to as a defining query.

After creating a view, you can query data from it as you would from a regular table. Behind the scenes, PostgreSQL will rewrite the query against the view and its defining query, executing it to retrieve data from the base tables.

Views do not store data except the materialized views. In PostgreSQL, you can create special views called materialized views that store data physically and periodically refresh it from the base tables.

The materialized views are handy in many various scenarios, providing faster data access to a remote server and serving as an effective caching mechanism.

## Materialized Views

PostgreSQL extends the view concept to the next level which allows views to store data physically. These views are called materialized views.

Materialized views cache the result sets of a complex and expensive query and allow you to refresh data periodically.

The materialized views can be useful in many cases that require fast data access. Therefore, you often find them in data warehouses and business intelligence applications.

    CREATE MATERIALIZED VIEW view_name
    AS
    query
    WITH [NO] DATA;

# PostgreSQL Triggers

A PostgreSQL trigger is a function invoked automatically whenever an event associated with a table occurs. An event could be any of the following: INSERT, UPDATE, DELETE or TRUNCATE.

A trigger is a special user-defined function associated with a table. To create a new trigger, you define a trigger function first, and then bind this trigger function to a table.

The difference between a trigger and a user-defined function is that a trigger is automatically invoked when a triggering event occurs.

PostgreSQL provides two main types of triggers:

Row-level triggers
Statement-level triggers.
The differences between the two kinds are how many times the trigger is invoked and at what time.

For example, if you issue an UPDATE statement that modifies 20 rows, the row-level trigger will be invoked 20 times, while the statement-level trigger will be invoked 1 time.