# All the answers are curated and hand-picked by Vipin Pro. Hope it helps your brain cells! 😂

## SET 1
#### a. Create tables Dept and Emp
CREATE TABLE Dept (
    deptno INT PRIMARY KEY,
    dname VARCHAR(50),
    loc VARCHAR(50)
);

CREATE TABLE Emp (
    empno INT PRIMARY KEY,
    ename VARCHAR(50),
    job VARCHAR(50),
    mgr INT,
    hiredate DATE,
    sal DECIMAL(10,2),
    deptno INT,
    FOREIGN KEY (deptno) REFERENCES Dept(deptno)
);

## b. Update salary for employees with 10+ years experience
UPDATE Emp
SET sal = sal * 1.15
WHERE DATEDIFF(YEAR, hiredate, GETDATE()) > 10;

#### c. Display employees in Bombay
SELECT e.*
FROM Emp e
JOIN Dept d ON e.deptno = d.deptno
WHERE d.loc = 'Bombay';

#### d. Manager with maximum employees
SELECT mgr, COUNT(*) as emp_count
FROM Emp
WHERE mgr IS NOT NULL
GROUP BY mgr
ORDER BY emp_count DESC
LIMIT 1;

#### e. Create view for employees and their managers
CREATE VIEW emp_mgr_view AS
SELECT e.ename as employee_name, m.ename as manager_name
FROM Emp e
LEFT JOIN Emp m ON e.mgr = m.empno;

## SET 2
#### Create tables (already created in SET 1)

#### i. Count clerks in company
SELECT COUNT(*) as clerk_count
FROM Emp
WHERE UPPER(job) = 'CLERK';

#### ii. Employees with 'LA' in name
SELECT *
FROM Emp
WHERE ename LIKE '%LA%';

#### iii. Employees with salary between 1000 and 5000
SELECT empno, ename, sal
FROM Emp
WHERE sal BETWEEN 1000 AND 5000;

#### iv. Employees in Chicago
SELECT e.*
FROM Emp e
JOIN Dept d ON e.deptno = d.deptno
WHERE UPPER(d.loc) = 'CHICAGO';

## SET 3
#### a. Employees earning more than their managers
SELECT e.ename
FROM Emp e
JOIN Emp m ON e.mgr = m.empno
WHERE e.sal > m.sal;

#### b. Employees with highest salary in their departments
SELECT e.*
FROM Emp e
WHERE sal = (
    SELECT MAX(sal)
    FROM Emp e2
    WHERE e2.deptno = e.deptno
);

#### c. Employees in same location
SELECT e1.ename, e2.ename, d.loc
FROM Emp e1
JOIN Emp e2 ON e1.empno < e2.empno
JOIN Dept d ON e1.deptno = d.deptno
WHERE e1.deptno = e2.deptno;

#### d. Employees with salary equal to min salary of any department
SELECT e.*
FROM Emp e
WHERE sal IN (
    SELECT MIN(sal)
    FROM Emp
    GROUP BY deptno
);

#### e. Departments with no employees
SELECT d.*
FROM Dept d
LEFT JOIN Emp e ON d.deptno = e.deptno
WHERE e.empno IS NULL;

## SET 4
#### Create Sales table
CREATE TABLE Sales (
    Sales_No INT PRIMARY KEY,
    Salesname VARCHAR(50),
    Branch VARCHAR(50),
    Salesamount DECIMAL(10,2),
    DOB DATE
);

#### i. Insert records
INSERT INTO Sales VALUES
(1, 'Vipin', 'North', 5000, '2020-12-15'),
(2, 'Eren Yeager', 'South', 6000, '2015-12-21'),
(3, 'Your Dad', 'East', 4500, '2006-06-10'),
(4, 'Harry', 'West', 7000, '2004-12-05'),
(5, 'GoJO', 'North', 5500, '2012-03-25');

#### ii. Total sales amount by branch
SELECT Branch, SUM(Salesamount) as total_sales
FROM Sales
GROUP BY Branch;

#### iii. Average sales amount by branch
SELECT Branch, AVG(Salesamount) as avg_sales
FROM Sales
GROUP BY Branch;

#### iv. December born salesmen
SELECT Salesname, FORMAT(DOB, 'dd-MMM-yy') as formatted_dob
FROM Sales
WHERE MONTH(DOB) = 12;

#### v. Display name and DOB ordered by month
SELECT Salesname, DOB
FROM Sales
ORDER BY MONTH(DOB), Salesname;

## SET 5
#### Create employee table
CREATE TABLE employee (
    empno INT PRIMARY KEY,
    name VARCHAR(50),
    department INT,
    salary DECIMAL(10,2),
    job VARCHAR(50)
);

#### i. Display employees in department 20
SELECT *
FROM employee
WHERE department = 20;

#### ii. Display records in descending salary order
SELECT *
FROM employee
ORDER BY salary DESC;

#### iii. Min, total, average salary by department
SELECT department,
    MIN(salary) as min_salary,
    SUM(salary) as total_salary,
    AVG(salary) as avg_salary
FROM employee
GROUP BY department;

#### iv. Count of different jobs
SELECT job, COUNT(*) as job_count
FROM employee
GROUP BY job;