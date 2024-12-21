
## SET 1
#### Create tables Employee and Department
CREATE TABLE Department (
    deptno INT PRIMARY KEY,
    dname VARCHAR(50),
    loc VARCHAR(50)
);

CREATE TABLE Employee (
    empno INT PRIMARY KEY,
    ename VARCHAR(50),
    job VARCHAR(50),
    mgr INT,
    hiredate DATE,
    sal DECIMAL(10,2),
    commission DECIMAL(10,2),
    deptno INT,
    FOREIGN KEY (deptno) REFERENCES Department(deptno)
);

#### i. Count number of clerks
SELECT COUNT(*) as clerk_count
FROM Employee
WHERE UPPER(job) = 'CLERK';

#### ii. Employees with 'LA' in name
SELECT ename
FROM Employee
WHERE ename LIKE '%LA%';

#### iii. Employees with salary between 1000 and 5000
SELECT empno, ename, sal
FROM Employee
WHERE sal BETWEEN 1000 AND 5000;

#### iv. Employees in Chicago
SELECT e.*
FROM Employee e
JOIN Department d ON e.deptno = d.deptno
WHERE UPPER(d.loc) = 'CHICAGO';

## SET 2
#### Create Employee table
CREATE TABLE Employee (
    empno INT PRIMARY KEY,
    name VARCHAR(50),
    department INT,
    salary DECIMAL(10,2),
    job VARCHAR(50)
);

#### i. Employees in department 20
SELECT *
FROM Employee
WHERE department = 20;

#### ii. Employees ordered by salary desc
SELECT *
FROM Employee
ORDER BY salary DESC;

#### iii. Department-wise salary statistics
SELECT department,
    MIN(salary) as min_salary,
    SUM(salary) as total_salary,
    AVG(salary) as avg_salary
FROM Employee
GROUP BY department;

#### iv. Count of different jobs
SELECT job, COUNT(*) as job_count
FROM Employee
GROUP BY job;

## SET 3
#### Create tables STUDENT and TEACHER
CREATE TABLE TEACHER (
    TeacherID INT PRIMARY KEY,
    Department VARCHAR(50),
    Name VARCHAR(50),
    City VARCHAR(50),
    Specialization VARCHAR(50)
);

CREATE TABLE STUDENT (
    StudentID INT PRIMARY KEY,
    Name VARCHAR(50),
    Programme VARCHAR(50),
    TutorID INT,
    FOREIGN KEY (TutorID) REFERENCES TEACHER(TeacherID)
);

#### b. Students with their tutor names
SELECT s.Name as student_name, t.Name as tutor_name
FROM STUDENT s
LEFT JOIN TEACHER t ON s.TutorID = t.TeacherID;

#### c. Count of teachers by department
SELECT Department, COUNT(*) as teacher_count
FROM TEACHER
GROUP BY Department;

#### d. Departments with more than 2 teachers
SELECT Department, COUNT(*) as teacher_count
FROM TEACHER
GROUP BY Department
HAVING COUNT(*) > 2;

#### e. Tutor names for BTech programme
SELECT DISTINCT t.Name as tutor_name
FROM TEACHER t
JOIN STUDENT s ON t.TeacherID = s.TutorID
WHERE s.Programme = 'Btech';

## SET 4
#### Create tables Employee and Dept
CREATE TABLE Dept (
    deptno INT PRIMARY KEY,
    dname VARCHAR(50),
    hod_id INT
);

CREATE TABLE Employee (
    empno INT PRIMARY KEY,
    name VARCHAR(50),
    departmentno INT,
    salary DECIMAL(10,2),
    job VARCHAR(50),
    FOREIGN KEY (departmentno) REFERENCES Dept(deptno)
);

#### b. Employees in department 20
SELECT *
FROM Employee
WHERE departmentno = 20;

#### c. Employees ordered by salary desc
SELECT *
FROM Employee
ORDER BY salary DESC;

#### d. Employees in Technical department with HOD details
SELECT e.*, h.name as hod_name
FROM Employee e
JOIN Dept d ON e.departmentno = d.deptno
JOIN Employee h ON d.hod_id = h.empno
WHERE d.dname = 'Technical';

#### e. Department-wise salary statistics
SELECT departmentno,
    MIN(salary) as min_salary,
    SUM(salary) as total_salary,
    AVG(salary) as avg_salary
FROM Employee
GROUP BY departmentno;

## SET 5
#### Create tables FACULTY and Dept
CREATE TABLE Dept (
    deptno INT PRIMARY KEY,
    dname VARCHAR(50),
    hod_id INT
);

CREATE TABLE FACULTY (
    Faculty_Id INT PRIMARY KEY,
    First_Name VARCHAR(50),
    Last_Name VARCHAR(50),
    Salary DECIMAL(10,2),
    Dno INT,
    FOREIGN KEY (Dno) REFERENCES Dept(deptno)
);

#### b. Count of faculties by department
SELECT d.dname, COUNT(*) as faculty_count
FROM FACULTY f
JOIN Dept d ON f.Dno = d.deptno
GROUP BY d.dname;

#### c. HODs and their departments
SELECT d.dname, CONCAT(f.First_Name, ' ', f.Last_Name) as hod_name
FROM Dept d
JOIN FACULTY f ON d.hod_id = f.Faculty_Id;

#### d. Faculties with above-average salary
SELECT First_Name, Last_Name, Salary
FROM FACULTY
WHERE Salary > (SELECT AVG(Salary) FROM FACULTY);

#### e. Departments with more than 4 faculties
SELECT d.dname, COUNT(*) as faculty_count
FROM FACULTY f
JOIN Dept d ON f.Dno = d.deptno
GROUP BY d.dname
HAVING COUNT(*) > 4;