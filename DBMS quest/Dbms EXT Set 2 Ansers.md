
# All the answers are curated and hand-picked by Vipin Pro. Hope it helps your brain cells! 😂
## SET 1
#### a. Create tables EMPLOYEE and DEPT
CREATE TABLE DEPT (
    deptno INT PRIMARY KEY,
    dname VARCHAR(50),
    loc VARCHAR(50)
);

CREATE TABLE EMPLOYEE (
    Employee_Id INT PRIMARY KEY,
    First_Name VARCHAR(50),
    Last_Name VARCHAR(50),
    Salary DECIMAL(10,2),
    Manager_Id INT,
    Dno INT,
    FOREIGN KEY (Dno) REFERENCES DEPT(deptno)
);

#### b. Find employees in Technical department
SELECT e.Employee_Id, e.First_Name, e.Last_Name, e.Salary
FROM EMPLOYEE e
JOIN DEPT d ON e.Dno = d.deptno
WHERE d.dname = 'Technical';

#### c. List employees under manager 100
SELECT *
FROM EMPLOYEE
WHERE Manager_Id = 100;

#### d. Find employees with salary >= 4800
SELECT *
FROM EMPLOYEE
WHERE Salary >= 4800;

#### e. List employees whose last name starts with A
SELECT *
FROM EMPLOYEE
WHERE Last_Name LIKE 'A%';

## SET 2
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
    comm DECIMAL(10,2),
    deptno INT
);

#### b. Add foreign key to Employee table
ALTER TABLE Emp
ADD CONSTRAINT fk_deptno
FOREIGN KEY (deptno) REFERENCES Dept(deptno);

#### c. Update salary for employees with 10+ years experience
UPDATE Emp
SET sal = sal * 1.15
WHERE DATEDIFF(YEAR, hiredate, GETDATE()) > 10;

#### d. Display manager with maximum employees
SELECT mgr, COUNT(*) as emp_count
FROM Emp
WHERE mgr IS NOT NULL
GROUP BY mgr
ORDER BY emp_count DESC
LIMIT 1;

#### e. Display employees in HR department
SELECT e.*
FROM Emp e
JOIN Dept d ON e.deptno = d.deptno
WHERE d.dname = 'HR';

## SET 3
#### a. Create tables STUDENT and TEACHER
CREATE TABLE TEACHER (
    Teacher_ID INT PRIMARY KEY,
    Department VARCHAR(50),
    Name VARCHAR(50),
    City VARCHAR(50),
    Specialization VARCHAR(50)
);

CREATE TABLE STUDENT (
    Student_ID INT PRIMARY KEY,
    Name VARCHAR(50),
    Programme VARCHAR(50),
    Teacher_ID INT,
    FOREIGN KEY (Teacher_ID) REFERENCES TEACHER(Teacher_ID)
);

#### b. Display teachers in Delhi
SELECT Name, Specialization
FROM TEACHER
WHERE City = 'Delhi';

#### c. Display teacher and their students
SELECT t.Name as Teacher_Name, s.Name as Student_Name
FROM TEACHER t
LEFT JOIN STUDENT s ON t.Teacher_ID = s.Teacher_ID
WHERE t.Teacher_ID = 123;

#### d. Display teachers in Physics department
SELECT Name, Specialization, City
FROM TEACHER
WHERE Department = 'Physics';

#### e. Count MCA students
SELECT COUNT(*) as mca_students
FROM STUDENT
WHERE Programme = 'MCA';

## SET 4
#### a. Tables are already defined in the question

#### b. Projects with budget > 100,000
SELECT pno, pname
FROM proj
WHERE budget > 100000;

#### c. Employees in D1 department ordered by decreasing salary
SELECT ename
FROM emp
WHERE dno = 'D1'
ORDER BY salary DESC;

#### d. Employees with title EE or SA and salary > 35,000
SELECT eno, ename
FROM emp
WHERE (title = 'EE' OR title = 'SA')
AND salary > 35000;