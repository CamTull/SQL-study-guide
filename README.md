# SQL-cheat-sheet
Most common SQL commands as reference with examples

## Manipulating a table

### Creating a table
```SQL
-- Creating a table + specifying data types
CREATE TABLE student (
student_id INT PRIMARY KEY,
student_name VARCHAR(50),
major VARCHAR(50)
)
```

### Deleting a table
```SQL
--Deleting a table
DROP TABLE student
```

### Altering fields/columns in a table
```SQL
--Altering a table (adding a field + specifying data type)
ALTER TABLE student ADD gpa DECIMAL(3, 2)

--Altering a table (deleting a column)
ALTER TABLE student DROP COLUMN gpa
```

### Inserting data into a table
```SQL
--Inserting data into table
INSERT INTO student VALUES (4, 'John', 'Philosophy')

--Inserting data into table (specifying which data types we have out of total required to avoid errors)
INSERT INTO student(student_id, student_name) VALUES(
	3,
	'Kate'
)
```

### Getting details about table

```SQL 
--Equivalent to DESCRIBE => details of table data types, etc.
EXEC sp_help student
```

## Basic Querying
```SQL
--Pulling all data from a table
SELECT *
FROM student

--Conditioning pull requests +
SELECT name, major
FROM student
WHERE student_id <= 3 AND firstname <> 'Jack'

-- Operators 
-- +, -, <=, >=, =, <>, AND, OR
```

## Constraints (MS SQL Server)

When creating a table, using the following (non exhaustive) constraints can help control what type of information makes it into the table.

* NOT NULL
* UNIQUE
* IDENTITY(starting #, incrementing #)
* DEFAULT 'default value'

🍯 Fun fact, PRIMARY KEY is setting one of table columns to both NOT NULL and UNIQUE

### Example (will auto-increment student_id by 1 starting at 1):

```SQL
CREATE TABLE student (
	student_id INT IDENTITY(1,1),
	firstname VARCHAR(50) NOT NULL,
	major VARCHAR(50) DEFAULT 'Undecided',
	PRIMARY KEY(student_id))
 
 INSERT INTO student(firstname, major) VALUES ('Jack', 'Math')
 INSERT INTO student(firstname, major) VALUES ('Kate', 'Sociology')
```

![image](https://user-images.githubusercontent.com/94760028/208919331-f24ff63b-dc1a-48c2-98df-55fd00584c4f.png)

## Updating & Deleting

```SQL
-- Will delete all rows from table
DELETE FROM student

-- Specifying row and/or conditions to delete
DELETE FROM student
WHERE student_id = 5

-- Two examples of syntax to update a table
-- WHERE used to set conditions
 UPDATE student
 SET major = 'Astro-Physics'
 WHERE major = 'Astrology' OR major = 'Physics'

 UPDATE student
 SET major = 'Physics'
 WHERE firstname = 'Adam'
```
