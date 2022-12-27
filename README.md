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

--Using IN operator is basically =
SELECT *
 FROM student
 WHERE firstname IN ('Jack', 'John') AND student_id > 2
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
## Creating a Complex Company Schema (ex. from Giraffe Academy)

```SQL
CREATE TABLE employee (
  emp_id INT PRIMARY KEY,
  first_name VARCHAR(40),
  last_name VARCHAR(40),
  birth_day DATE,
  sex VARCHAR(1),
  salary INT,
  super_id INT,
  branch_id INT
);

CREATE TABLE branch (
  branch_id INT PRIMARY KEY,
  branch_name VARCHAR(40),
  mgr_id INT,
  mgr_start_date DATE,
  FOREIGN KEY(mgr_id) REFERENCES employee(emp_id) ON DELETE SET NULL
);

ALTER TABLE employee
ADD FOREIGN KEY(branch_id)
REFERENCES branch(branch_id)
ON DELETE SET NULL;

ALTER TABLE employee
ADD FOREIGN KEY(super_id)
REFERENCES employee(emp_id)
ON DELETE NO ACTION;

CREATE TABLE client (
  client_id INT PRIMARY KEY,
  client_name VARCHAR(40),
  branch_id INT,
  FOREIGN KEY(branch_id) REFERENCES branch(branch_id) ON DELETE SET NULL
);

CREATE TABLE works_with (
  emp_id INT,
  client_id INT,
  total_sales INT,
  PRIMARY KEY(emp_id, client_id),
  FOREIGN KEY(emp_id) REFERENCES employee(emp_id) ON DELETE CASCADE,
  FOREIGN KEY(client_id) REFERENCES client(client_id) ON DELETE CASCADE
);

CREATE TABLE branch_supplier (
  branch_id INT,
  supplier_name VARCHAR(40),
  supply_type VARCHAR(40),
  PRIMARY KEY(branch_id, supplier_name),
  FOREIGN KEY(branch_id) REFERENCES branch(branch_id) ON DELETE CASCADE
);

-- Corporate
INSERT INTO employee VALUES(100, 'David', 'Wallace', '1967-11-17', 'M', 250000, NULL, NULL);

INSERT INTO branch VALUES(1, 'Corporate', 100, '2006-02-09');

UPDATE employee
SET branch_id = 1
WHERE emp_id = 100;

INSERT INTO employee VALUES(101, 'Jan', 'Levinson', '1961-05-11', 'F', 110000, 100, 1);

-- Scranton
INSERT INTO employee VALUES(102, 'Michael', 'Scott', '1964-03-15', 'M', 75000, 100, NULL);

INSERT INTO branch VALUES(2, 'Scranton', 102, '1992-04-06');

UPDATE employee
SET branch_id = 2
WHERE emp_id = 102;

INSERT INTO employee VALUES(103, 'Angela', 'Martin', '1971-06-25', 'F', 63000, 102, 2);
INSERT INTO employee VALUES(104, 'Kelly', 'Kapoor', '1980-02-05', 'F', 55000, 102, 2);
INSERT INTO employee VALUES(105, 'Stanley', 'Hudson', '1958-02-19', 'M', 69000, 102, 2);

-- Stamford
INSERT INTO employee VALUES(106, 'Josh', 'Porter', '1969-09-05', 'M', 78000, 100, NULL);

INSERT INTO branch VALUES(3, 'Stamford', 106, '1998-02-13');

UPDATE employee
SET branch_id = 3
WHERE emp_id = 106;

INSERT INTO employee VALUES(107, 'Andy', 'Bernard', '1973-07-22', 'M', 65000, 106, 3);
INSERT INTO employee VALUES(108, 'Jim', 'Halpert', '1978-10-01', 'M', 71000, 106, 3);


-- BRANCH SUPPLIER
INSERT INTO branch_supplier VALUES(2, 'Hammer Mill', 'Paper');
INSERT INTO branch_supplier VALUES(2, 'Uni-ball', 'Writing Utensils');
INSERT INTO branch_supplier VALUES(3, 'Patriot Paper', 'Paper');
INSERT INTO branch_supplier VALUES(2, 'J.T. Forms & Labels', 'Custom Forms');
INSERT INTO branch_supplier VALUES(3, 'Uni-ball', 'Writing Utensils');
INSERT INTO branch_supplier VALUES(3, 'Hammer Mill', 'Paper');
INSERT INTO branch_supplier VALUES(3, 'Stamford Lables', 'Custom Forms');

-- CLIENT
INSERT INTO client VALUES(400, 'Dunmore Highschool', 2);
INSERT INTO client VALUES(401, 'Lackawana Country', 2);
INSERT INTO client VALUES(402, 'FedEx', 3);
INSERT INTO client VALUES(403, 'John Daly Law, LLC', 3);
INSERT INTO client VALUES(404, 'Scranton Whitepages', 2);
INSERT INTO client VALUES(405, 'Times Newspaper', 3);
INSERT INTO client VALUES(406, 'FedEx', 2);

-- WORKS_WITH
INSERT INTO works_with VALUES(105, 400, 55000);
INSERT INTO works_with VALUES(102, 401, 267000);
INSERT INTO works_with VALUES(108, 402, 22500);
INSERT INTO works_with VALUES(107, 403, 5000);
INSERT INTO works_with VALUES(108, 403, 12000);
INSERT INTO works_with VALUES(105, 404, 33000);
INSERT INTO works_with VALUES(107, 405, 26000);
INSERT INTO works_with VALUES(102, 406, 15000);
INSERT INTO works_with VALUES(105, 406, 130000);

Select *
FROM employee

SELECT *
FROM branch

SELECT *
FROM client

SELECT *
FROM works_with

SELECT *
FROM branch_supplier
```

### Playing with company data..

```SQL
--Select all employees from employee table in descending order by salary
Select *
FROM employee
ORDER BY salary DESC

--Find all employees ordered by sex then name
Select *
FROM employee
ORDER BY sex, first_name, last_name

--Find first 5 employees in the table
SELECT TOP 5 *
FROM employee

--Find first and last names of all employees
SELECT first_name, last_name
FROM employee

--Find and concatenate first and last names of all employees
SELECT first_name + ' ' + last_name AS full_name
FROM employee

--Find out all different genders
SELECT DISTINCT sex
FROM employee
```

### SQL 'Functions'

```SQL
--Find the number of employees
SELECT COUNT(emp_id) AS employeeNum
FROM employee

--Find the number of female employees born after 1970
SELECT COUNT(emp_id) expfem
FROM employee
WHERE sex = 'F' AND birth_day > '1970-01-01'

--Find the average of all employee's salaries
SELECT AVG(salary)
FROM employee

--Find sum of employee's salaries
SELECT SUM(salary)
FROM employee

--Find out how many males and females there are
SELECT COUNT(sex), sex
FROM employee
GROUP BY sex

--Find total sales of each salesperson
SELECT SUM(total_sales) AS totalsales, emp_id
FROM works_with
GROUP BY emp_id
```

### Wildcards

```SQL
-- % = any number of characters, _ = 1 character

-- Find any client's who are an LLC
SELECT *
FROM client
WHERE client_name LIKE '%LLC'

-- Find any branch suppliers who are in the label business
SELECT *
FROM branch_supplier
WHERE supplier_name LIKE '% label%'

-- Find any employees born in October
-- (4 x _ ) followed by numerical month (10) followed by anything (%) 
SELECT *
FROM employee
WHERE birth_day LIKE '____-10%'
```

### Unions

```SQL
```
