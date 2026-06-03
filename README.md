# library-PROJECT_p2

# Library Management System using SQL Project --P2

## Project Overview

**Project Title**: Library Management System  
**Level**: Intermediate  
**Database**: `library_db`

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing advanced SQL queries. The goal is to showcase skills in database design, manipulation, and querying.



## Objectives

1. **Set up the Library Management System Database**: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
2. **CRUD Operations**: Perform Create, Read, Update, and Delete operations on the data.
3. **CTAS (Create Table As Select)**: Utilize CTAS to create new tables based on query results.
4. **Advanced SQL Queries**: Develop complex queries to analyze and retrieve specific data.

## Project Structure

### 1. Database Setup


- **Database Creation**: Created a database named project_p2;.
- **Table Creation**: Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.

```sql
USE project_p2;

-- DROP TABLE IF EXISTS branch;


create table branch (branch_id varchar(10) PRIMARY KEY ,
	manager_id varchar(10),
	branch_address varchar(15),
	contact_no varchar(10));
    
    -- DROP table IF EXISTS eamployees;
    
CREATE TABLE employees (
    emp_id VARCHAR(15) primary KEY,
    emp_name VARCHAR(10),
    position VARCHAR(10),
    salary INT,
    branch_id VARCHAR(10)
);
--  DROP table IF EXISTS issued_status;


CREATE table issued_status
(issued_id varchar(10) PRIMARY KEY,
	issued_member_id varchar(10),
	issued_book_name varchar(30),
	issued_date	DATE ,
    issued_book_isbn VARCHAR(10),
	issued_emp_id VARCHAR (10));
    
    
    -- =MAX(LEN(B2:B36)) excel ,
    
  CREATE TABLE books (
    isbn VARCHAR(20)PRIMARY KEY ,
    book_title VARCHAR(60),
    category VARCHAR(15),
    rental_price FLOAT,
    status VARCHAR(15),
    author VARCHAR(35),
    publisher VARCHAR(35)
);


CREATE TABLE members (
    member_id VARCHAR(10) PRIMARY KEY,
    member_name VARCHAR(35),
    member_address VARCHAR(35),
    reg_date DATE
);



-- Drop the existing table first

-- DROP TABLE IF EXISTS issued_status;

CREATE TABLE issued_status (
    issued_id VARCHAR(10) PRIMARY KEY,
    issued_member_id VARCHAR(10),
    issued_book_name VARCHAR(50),
    issued_date DATE,
    issued_book_isbn VARCHAR(35),
    issued_emp_id VARCHAR(10)
);



```

### 2. CRUD Operations

- **Create**: Inserted sample records into the `books` table.
- **Read**: Retrieved and displayed data from various tables.
- **Update**: Updated records in the `employees` table.
- **Delete**: Removed records from the `members` table as needed.

**Task 1. Create a New Book Record**
-- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"

```sql

INSERT INTO books
VALUES
('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
```
**Task 2: Update an Existing Member's Address**

```sql
UPDATE members
set member_address = '125 Oak St'
where member_id = 'C103';
```

**Task 3: Delete a Record from the Issued Status Table**
-- Objective: Delete the record with issued_id = 'IS121' from the issued_status table.

```sql
DELETE FROM issued_status
where issued_id = 'IS121';
```

**Task 4: Retrieve All Books Issued by a Specific Employee**
-- Objective: Select all books issued by the employee with emp_id = 'E101'.
```sql
SELECT * FROM issued_status
where issued_emp_id ='E101';
```


**Task 5: List Members Who Have Issued More Than One Book**
-- Objective: Use GROUP BY to find members who have issued more than one book.

```sql
SELECT issued_emp_id,
count(*)
FROM issued_status
group by 1
HAVING count(*) > 1;
```

### 3. CTAS (Create Table As Select)

- **Task 6: Create Summary Tables**: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt**

```sql
SELECT *  FROM
     books AS b
JOIN
     issued_status AS ist 
ON 
     ist.issued_book_isbn = b.isbn;
     




   CREATE TABLE book_issued_cnt AS
SELECT b.isbn, b.book_title, COUNT(ist.issued_id) AS issue_count
FROM issued_status as ist
JOIN books as b
ON ist.issued_book_isbn = b.isbn
GROUP BY b.isbn, b.book_title;
```


### 4. Data Analysis & Findings

The following SQL queries were used to address specific questions:

Task 7. **Retrieve All Books in a Specific Category**:

```sql
SELECT * FROM books 
where 
category = 'classic';
```

8. **Task 8: Find Total Rental Income by Category**:

```sql
select * from books;

SELECT b.category, sum(rental_price),COUNT(*)
FROM issued_status AS ist 
JOIN 
books AS b
ON b.isbn = ist.issued_book_isbn
group by 1;

```

9. **List Members Who Registered in the Last 180 Days**:
```sql

SELECT * FROM members
WHERE reg_date >= CURRENT_DATE - INTERVAL 180 DAY;

SELECT current_date;

```

10. **List Employees with Their Branch Manager's Name and their branch details**:

```sql
SELECT 
    e1.emp_id,
    e1.emp_name,
    e1.position,
    e1.salary,
    b.*,
    e2.emp_name AS manager
FROM
    employees AS e1
        JOIN
    branch AS b ON e1.branch_id = b.branch_id
        JOIN
    employees AS e2 ON e2.emp_id = b.manager_id;
```

Task 11. **Create a Table of Books with Rental Price Above a Certain Threshold**:
```sql
 CREATE TABLE expensive_books AS
    SELECT * FROM books
    WHERE rental_price >= 7;
```

Task 12: **Retrieve the List of Books Not Yet Returned**
```sql
SELECT DISTINCT issued_book_name FROM issued_status AS ist
    left JOIN  
    return_status AS rs 
    On rs.issued_id = ist.issued_id
    WHERE rs.return_id IS NULL;
```

## Advanced SQL Operations

**Task 13: Identify Members with Overdue Books**  
Write a query to identify members who have overdue books (assume a 30-day return period). Display the member's_id, member's name, book title, issue date, and days overdue.

```sql
 select CURRENT_DATE;
 
select 
ist.issued_member_id,
m.member_name,
bk.book_title,
ist.issued_date,
-- rs.return_date,
current_date - ist.issued_date AS overdew
FROM 
issued_status as ist 
JOIN 
members as m
ON m.member_id = ist.issued_member_id
JOIN
books AS bk
ON bk.isbn = ist.issued_book_isbn
LEFT  JOIN 
return_status AS rs
ON rs.issued_id = ist.issued_id
WHERE rs.return_date IS null
AND 
(current_date - ist.issued_date)  > 30
ORDER by 1;
```


**Task 14: Update Book Status on Return**  
Write a query to update the status of books in the books table to "Yes" when they are returned (based on entries in the return_status table).


```sql



-- how to perform manually 

SELECT * FROM books;
SELECT * FROM return_status;
SELECT * FROM issued_status;
-- so to perform the task, we have to firstly go to the issue status table where we find the issue book ISBN ,
--  secondly, we have to go to the return table where we have to check that the book was written or not,
-- and the third step is about to update the status in book to know two years when the book was writtend
 UPDATE books-- for testing the update
    SET status = 'no'
    WHERE isbn ='978-0-451-52994-2';
    
    -- to see the record was change in books table
Select * from  books
Where isbn = '978-0-451-52994-2';
    
-- to get the members ID and the issued ID of the person
SELECT * FROM issued_status
WHERE issued_book_isbn = '978-0-451-52994-2';
    
-- to see the person is written, a book or not, whose ID is IS 130
    
    SELECT * from return_status
    WHERE issued_id = 'IS130';
    -- just to see the members name from ID, which we get from issued table 
    
    SELECT * FROM members
    WHERE member_id = 'C106';
    -- ,,,,,,,,,,,,,,,,,,,,,,
    -- to do the insult query manually
    -- first windsor, the data into return table after we updated into book table
    Insert into return_status (return_id, issued_id,return_date)
    VALUES ('RS125','IS130',CURRENT_DATE);
    
    - -- update data into book , no  to yes after books was riturned , we get ISBN from issued_table
    
    
UPDATE books
    SET status = 'yes'
    WHERE isbn ='978-0-451-52994-2';




    -- there were used store procedure to update automatically when we added to the return_status table 
  --   SHOW PROCEDURE STATUS WHERE Name = 'add_return_record';
-- DROP PROCEDURE IF EXISTS add_return_record;


DELIMITER //

CREATE PROCEDURE add_return_record 
(p_return_id varchar(15),
p_issued_id VARCHAR(30),
P_return_date date )
BEGIN
DECLARE v_isbn VARCHAR (50);
 DECLARE v_book_name VARCHAR (80);
 -- there were written are the logic we want to done
INSERT into return_status (return_id, issued_id,return_date)
VALUES (p_return_id,p_issued_id,CURRENT_DATE);

SELECT issued_book_isbn ,
issued_book_name
 INTO
 v_isbn,
 v_book_name
FROM issued_status
WHERE issued_id = p_issued_id;

  UPDATE books
    SET status = 'yes'
    WHERE isbn = v_isbn;
SELECT CONCAT('Thank you for returning the book: ', v_book_name);
END //

DELIMITER ; 
					  
    
    DELIMITER //


    -- testing the functions
SELECT * FROM books
WHERE isbn = '978-0-307-58837-1';

SELECT * FROM issued_status
WHERE issued_book_isbn = '978-0-307-58837-1';

SELECT * FROM return_status
WHERE issued_id = 'IS135';
    
    
 -- calling function 
CALL add_return_record('RS138', 'IS135', CURDATE());    
SHOW PROCEDURE STATUS
WHERE Name LIKE '%return%';


CALL add_return_record('RS148', 'IS140', CURDATE());

-- to check that golf function are working or not
SELECT * FROM return_status
WHERE return_id = 'RS148'; -- yes it working


```




**Task 15: Branch Performance Report**  
Create a query that generates a performance report for each branch, showing the number of books issued, the number of books returned, and the total revenue generated from book rentals.

```sql
-- issued_status = employees through 'branch_id' = branch = return_status = books



CREATE TABLE Branch_Performance_Report
AS
SELECT 
b.branch_id,
b.manager_id,
COUNT(ist.issued_id) AS no_of_books_issued,
COUNT(rs.return_id) AS no_of_book_returned,
SUM(bk.rental_price) AS total_revanue
FROM issued_status as ist
JOIN 
employees as e
ON e.emp_id = ist.issued_emp_id
JOIN 
branch AS b
ON b.branch_id = e.branch_id
LEFT JOIN 
return_status AS rs
ON rs.issued_id = ist.issued_id
JOIN
books AS bk
ON bk.isbn = ist.issued_book_isbn
GROUP BY 1, 2;


```

**Task 16: CTAS: Create a Table of Active Members**  
Use the CREATE TABLE AS (CTAS) statement to create a new table active_members containing members who have issued at least one book in the last 2 months.

```sql
-- SELECT COUNT(*) FROM members;
-- SELECT MIN(issued_date), MAX(issued_date) FROM issued_status;


drop table active_members;
CREATE TABLE active_members AS
SELECT DISTINCT m.*
FROM 
members AS m
JOIN 
issued_status AS i
ON m.member_id = i.issued_member_id
WHERE i.issued_date >= (
    SELECT DATE_SUB(MAX(issued_date), INTERVAL 2 MONTH)
    FROM issued_status
);
-- because that is from 2024, so we use the previous syntax

-- this syntax for the current data for the current date

				 -- CREATE TABLE active_members_2 AS
				 -- SELECT *
				 -- WHERE member_id IN (
				--  SELECT DISTINCT issued_member_id
				-- FROM issued_status
				-- WHERE issued_date >= CURRENT_DATE - INTERVAL 2 MONTH);

-- DROP TABLE active_members_2;
-- SELECT * FROM active_members_2;


```


**Task 17: Find Employees with the Most Book Issues Processed**  
Write a query to find the top 3 employees who have processed the most book issues. Display the employee name, number of books processed, and their branch.

```sql
SELECT 
    e.emp_name,
    b.*,
    COUNT(ist.issued_id) AS no_of_book_issued
FROM issued_status AS ist
JOIN employees AS e
    ON ist.issued_emp_id = e.emp_id
JOIN branch AS b
    ON e.branch_id = b.branch_id
GROUP BY 1,2
ORDER BY no_of_book_issued DESC
LIMIT 3;
 

```

**Task 18: Stored Procedure**
Objective:
Create a stored procedure to manage the status of books in a library system.
Description:
Write a stored procedure that updates the status of a book in the library based on its issuance. The procedure should function as follows:
The stored procedure should take the book_id as an input parameter.
The procedure should first check if the book is available (status = 'yes').
If the book is available, it should be issued, and the status in the books table should be updated to 'no'.
If the book is not available (status = 'no'), the procedure should return an error message indicating that the book is currently not available.

```sql

 DROP PROCEDURE IF EXISTS issue_book;

DELIMITER //

CREATE PROCEDURE issue_book(
    IN p_book_id VARCHAR(20)
)
BEGIN
    DECLARE v_status VARCHAR(10);

    -- Get current status
    SELECT status
    INTO v_status
    FROM books
    WHERE isbn = p_book_id;

    -- Check availability
    IF v_status = 'yes' THEN

        UPDATE books
        SET status = 'no'
        WHERE isbn = p_book_id;

        SELECT 'Book issued successfully' AS message;

    ELSE

        SELECT 'Book is currently not available' AS message;

    END IF;

END //

DELIMITER ;
 CALL issue_book('978-1-60129-456-2');


```

## Reports

- **Database Schema**: Detailed table structures and relationships.
- **Data Analysis**: Insights into book categories, employee salaries, member registration trends, and issued books.
- **Summary Reports**: Aggregated data on high-demand books and employee performance.

## Conclusion

This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and advanced querying, providing a solid foundation for data management and analysis.

## How to Use
1. **Set Up the Database**: Execute the SQL scripts in the `database_setup.sql` file to create and populate the database.
2. **Run the Queries**: Use the SQL queries in the `analysis_queries.sql` file to perform the analysis.
3. **Explore and Modify**: Customize the queries as needed to explore different aspects of the data or answer additional questions.

## ABHIJEET KIShAN

This project showcases SQL skills essential for database management and analysis. For more content on SQL and data analysis, connect with me through the following channels:









Thank you for your interest in this project!









Project Conclusion

This Library Management System project helped in understanding how SQL is used to manage and analyze real-world data. Through this project, I learned to create and manage databases, tables, relationships, stored procedures, and queries. I performed data analysis using joins, aggregate functions, subqueries, CTEs, and stored procedures to generate meaningful insights such as active members, book issuance trends, branch performance, and book availability. The project improved my practical SQL skills and demonstrated how databases can be used to efficiently manage library operations and support data-driven decision-making.

IN Short 

This project strengthened my SQL fundamentals by working with real-world library data. I learned database design, joins, aggregations, stored procedures, reporting, and business analysis using SQL queries. It gave me hands-on experience in solving practical business problems through data.

Key Learnings

* Database and table creation
* Primary and foreign keys
* Joins (INNER, LEFT)
* Aggregate functions (COUNT, SUM, AVG)
* Subqueries and CTEs
* Stored Procedures
* Data analysis and reporting
* Business problem-solving using SQL
* Preparing data for dashboards in Power BI

This is a strong conclusion for a fresher Business Analyst, Data Analyst, or SQL Developer portfolio project.
