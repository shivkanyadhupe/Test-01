1.
1.Table creation:
CREATE TABLE Employee (
    employee_name VARCHAR(50),
    street VARCHAR(100),
    city VARCHAR(50),
    Id INT,
    Mobile_no VARCHAR(20),
    PRIMARY KEY (employee_name)
);

CREATE TABLE Works (
    employee_name VARCHAR(50),
    company_name VARCHAR(50),
    salary DECIMAL(10, 2),
    PRIMARY KEY (employee_name, company_name),
    FOREIGN KEY (employee_name) REFERENCES Employee(employee_name)
);

CREATE TABLE Company (
    company_name VARCHAR(50),
    city VARCHAR(50),
    PRIMARY KEY (company_name)
);

CREATE TABLE Manages (
    employee_name VARCHAR(50),
    manager_name VARCHAR(50),
    PRIMARY KEY (employee_name),
    FOREIGN KEY (employee_name) REFERENCES Employee(employee_name),
    FOREIGN KEY (manager_name) REFERENCES Employee(employee_name)
);
2.View creation:
CREATE VIEW EmployeeView AS
SELECT e.employee_name, e.street, e.city, e.Id, e.Mobile_no, w.company_name, w.salary, c.city AS company_city, m.manager_name
FROM Employee e
LEFT JOIN Works w ON e.employee_name = w.employee_name
LEFT JOIN Company c ON w.company_name = c.company_name
LEFT JOIN Manages m ON e.employee_name = m.employee_name;

3.Index creation:
CREATE INDEX idx_employee_name ON Employee (employee_name);
CREATE INDEX idx_company_name ON Company (company_name);

4.Sequence creation:
CREATE SEQUENCE employee_id_seq START WITH 1 INCREMENT BY 1;
5.Synonym creation:
CREATE SYNONYM emp FOR Employee;
6.Table or view deletion:

DROP TABLE Employee;
DROP VIEW EmployeeView;
7.Committing changes:
COMMIT;








2.
1.Table creation:
CREATE TABLE Employee (
    employee_name VARCHAR(50),
    street VARCHAR(100),
    city VARCHAR(50),
    Id INT,
    Mobile_no VARCHAR(20),
    PRIMARY KEY (employee_name)
);

CREATE TABLE Works (
    employee_name VARCHAR(50),
    company_name VARCHAR(50),
    salary DECIMAL(10, 2),
    PRIMARY KEY (employee_name, company_name),
    FOREIGN KEY (employee_name) REFERENCES Employee(employee_name)
);

CREATE TABLE Company (
    company_name VARCHAR(50),
    city VARCHAR(50),
    PRIMARY KEY (company_name)
);

CREATE TABLE Manages (
    employee_name VARCHAR(50),
    manager_name VARCHAR(50),
    PRIMARY KEY (employee_name),
    FOREIGN KEY (employee_name) REFERENCES Employee(employee_name),
    FOREIGN KEY (manager_name) REFERENCES Employee(employee_name)
);
2.View creation:
CREATE VIEW EmployeeView AS
SELECT e.employee_name, e.street, e.city, e.Id, e.Mobile_no, w.company_name, w.salary, c.city AS company_city, m.manager_name
FROM Employee e
LEFT JOIN Works w ON e.employee_name = w.employee_name
LEFT JOIN Company c ON w.company_name = c.company_name
LEFT JOIN Manages m ON e.employee_name = m.employee_name;
3.Index creation:
CREATE INDEX idx_employee_name ON Employee (employee_name);
CREATE INDEX idx_company_name ON Company (company_name);
4.Sequence creation:
CREATE SEQUENCE employee_id_seq START WITH 1 INCREMENT BY 1;
5.Synonym creation:
CREATE SYNONYM emp FOR Employee;
6.Table or view deletion:
DROP TABLE Employee;
DROP TABLE Works;
DROP TABLE Company;
DROP TABLE Manages;
7.Committing changes:
COMMIT;








3.
1.Join:
-- Inner Join
SELECT Employee.employee_name, Works.company_name, Works.salary
FROM Employee
INNER JOIN Works ON Employee.employee_name = Works.employee_name;

-- Left Join
SELECT Employee.employee_name, Works.company_name, Works.salary
FROM Employee
LEFT JOIN Works ON Employee.employee_name = Works.employee_name;

-- Right Join
SELECT Employee.employee_name, Works.company_name, Works.salary
FROM Employee
RIGHT JOIN Works ON Employee.employee_name = Works.employee_name;

-- Full Outer Join (not supported in all databases)
SELECT Employee.employee_name, Works.company_name, Works.salary
FROM Employee
FULL OUTER JOIN Works ON Employee.employee_name = Works.employee_name;
2.Sub-Query:
SELECT employee_name, company_name, salary
FROM Works
WHERE employee_name IN (SELECT employee_name FROM Manages WHERE manager_name = 'John');
3.View:
CREATE VIEW EmployeeView AS
SELECT e.employee_name, e.street, e.city, e.Id, e.Mobile_no, w.company_name, w.salary, c.city AS company_city, m.manager_name
FROM Employee e
LEFT JOIN Works w ON e.employee_name = w.employee_name
LEFT JOIN Company c ON w.company_name = c.company_name
LEFT JOIN Manages m ON e.employee_name = m.employee_name;

SELECT * FROM EmployeeView;



























4.
-- Create the Student table
CREATE TABLE Student (
    Roll_no INT,
    Name VARCHAR(50),
    Marks INT,
    Remark VARCHAR(10)
);

-- Insert sample data into the Student table
INSERT INTO Student (Roll_no, Name, Marks, Remark)
VALUES (1, 'John', 80, '');

INSERT INTO Student (Roll_no, Name, Marks, Remark)
VALUES (2, 'Jane', 30, '');

-- Create a stored procedure to update the remark of a student based on marks
CREATE OR REPLACE PROCEDURE UpdateRemark(p_Roll_no IN INT) IS
    v_Marks INT;
BEGIN
    -- Get the marks for the given Roll_no
    SELECT Marks INTO v_Marks
    FROM Student
    WHERE Roll_no = p_Roll_no;
    
    -- Update the remark based on the marks
    IF v_Marks >= 40 THEN
        UPDATE Student
        SET Remark = 'pass'
        WHERE Roll_no = p_Roll_no;
    ELSE
        UPDATE Student
        SET Remark = 'fail'
        WHERE Roll_no = p_Roll_no;
    END IF;
    
    -- Commit the changes
    COMMIT;
    
    -- Display the updated student record
    SELECT * FROM Student WHERE Roll_no = p_Roll_no;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Student not found.');
END;
/

-- Call the stored procedure to update the remark of student with Roll_no = 2
BEGIN
    UpdateRemark(2);
END;
/










5.
-- Create the Student table
CREATE TABLE Student (
    Roll_no INT,
    Name VARCHAR(50),
    Address VARCHAR(100),
    Marks INT,
    Grade VARCHAR(2),
    Active CHAR(1)
);

-- Insert sample data into the Student table
INSERT INTO Student (Roll_no, Name, Address, Marks, Grade, Active)
VALUES (1, 'John', '123 Main Street', 85, 'A', 'Y');

INSERT INTO Student (Roll_no, Name, Address, Marks, Grade, Active)
VALUES (2, 'Jane', '456 Elm Street', 65, 'B', 'Y');

-- Create the BEFORE DELETE trigger to prevent deletion of active students
CREATE OR REPLACE TRIGGER PreventDeleteActiveStudent
BEFORE DELETE ON Student
FOR EACH ROW
BEGIN
    IF :old.Active = 'Y' THEN
        RAISE_APPLICATION_ERROR(-20001, 'Cannot delete an active student.');
    END IF;
END;
/

-- Create the AFTER DELETE trigger to update the grade of the remaining students
CREATE OR REPLACE TRIGGER UpdateRemainingStudentsGrade
AFTER DELETE ON Student
FOR EACH ROW
BEGIN
    UPDATE Student
    SET Grade = 'C'
    WHERE Roll_no IN (
        SELECT Roll_no
        FROM Student
    );
    
    -- Commit the changes
    COMMIT;
END;
/

-- Try deleting a non-active student record
DELETE FROM Student WHERE Roll_no = 1;

-- Try deleting an active student record (will result in an error)
DELETE FROM Student WHERE Roll_no = 2;











6.
1.Create (Insert) operation:
// Insert a document into a collection
db.students.insertOne({
  name: "John Doe",
  age: 25,
  grade: "A"
});

2.Read (Select) operation:
// Find all documents in a collection
db.students.find();

// Find documents that match specific criteria
db.students.find({ age: { $gt: 20 } }); // Find students older than 20

// Find a single document
db.students.findOne({ name: "John Doe" });

3.Update operation:
// Update a document
db.students.updateOne(
  { name: "John Doe" },
  { $set: { grade: "B" } }
);

// Update multiple documents
db.students.updateMany(
  { age: { $gt: 20 } },
  { $inc: { age: 1 } }
);
4.Delete operation:
// Delete a document
db.students.deleteOne({ name: "John Doe" });

// Delete multiple documents
db.students.deleteMany({ age: { $lt: 20 } });

























7.
1.Get complete details from Duty_allocation:
SELECT *
FROM Duty_allocation;
2.Get duty allocation details for Emp_no 123461 for the month of April 1986:
SELECT *
FROM Duty_allocation
WHERE emp_no = 123461
  AND EXTRACT(MONTH FROM day) = 4
  AND EXTRACT(YEAR FROM day) = 1986;
3.Find the shift details for employee 'XYZ':
SELECT da.emp_no, da.day, da.shift
FROM Duty_allocation da
JOIN Employee e ON da.emp_no = e.emp_no
WHERE e.name = 'XYZ';
















8.
1.Get employee number of employees working on a project:
SELECT COUNT(*) AS EmployeeCount
FROM Assigned_To
WHERE Project_id = 'project_id';
2.Get details of employees working on project C353:
SELECT e.Emp_id, e.Emp_name
FROM Employee e
JOIN Assigned_To at ON e.Emp_id = at.emp_id
WHERE at.Project_id = 'C353';
3.Obtain details of employees working on both C353 & C354:
SELECT e.Emp_id, e.Emp_name
FROM Employee e
JOIN Assigned_To at ON e.Emp_id = at.emp_id
WHERE at.Project_id IN ('C353', 'C354')
GROUP BY e.Emp_id, e.Emp_name
HAVING COUNT(DISTINCT at.Project_id) = 2;














9.
1.Find the name of all employees who work for FBC:
SELECT e.employee_name
FROM Employee e
JOIN Works w ON e.employee_name = w.employee_name
JOIN Company c ON w.company_name = c.company_name
WHERE c.company_name = 'FBC';

2.Find the name and cities of all employees who work for FBC:
SELECT e.employee_name, e.city
FROM Employee e
JOIN Works w ON e.employee_name = w.employee_name
JOIN Company c ON w.company_name = c.company_name
WHERE c.company_name = 'FBC';

3.Find the names, street address, and cities of residence of all employees who work for FBC and earn more than $10,000:
SELECT e.employee_name, e.street, e.city
FROM Employee e
JOIN Works w ON e.employee_name = w.employee_name
JOIN Company c ON w.company_name = c.company_name
WHERE c.company_name = 'FBC'
  AND w.salary > 10000;
