### SQL Query to find max salary of Employee.  
```
SELECT MAX(salary) AS max_salary FROM Employee;

SELECT * FROM Employee WHERE salary = (SELECT MAX(salary) FROM Employee);
```

### SQL Query to find second highest salary of Employee
Method 1: Using DISTINCT and LIMIT (MySQL, PostgreSQL)  
```
SELECT DISTINCT salary FROM Employee ORDER BY salary DESC LIMIT 1 OFFSET 1;
```
- LIMIT 1 OFFSET 1 skips the highest and picks the second highest.

- Works only in SQL engines that support LIMIT.

Method 2: Using Subquery
```
SELECT MAX(salary) AS second_highest_salary FROM Employee WHERE salary < (SELECT MAX(salary) FROM Employee);
```

### SQL Query to find third highest salary of Employee
Method 1: Using DISTINCT, ORDER BY, LIMIT (MySQL, PostgreSQL)  
```
SELECT DISTINCT salary FROM Employee ORDER BY salary DESC LIMIT 1 OFFSET 2;
```
Method 2: Using Subqueries
```
SELECT MAX(salary) AS third_highest_salary FROM Employee
WHERE salary < (
    SELECT MAX(salary)
    FROM Employee
    WHERE salary < (
        SELECT MAX(salary)
        FROM Employee
    )
);
```
### How to find duplicate records in a table on database
```
SELECT email, COUNT(*) AS count FROM Employee GROUP BY email HAVING COUNT(*) > 1;

SELECT email, COUNT(email) AS count FROM Employee GROUP BY email HAVING count > 1;
```
### Department Highest Salary
```
SELECT d.name AS Department, e.name AS Employee, e.salary AS Salary FROM Employee e
JOIN Department d ON e.departmentId = d.id
WHERE (e.departmentId, e.salary) IN (
    SELECT departmentId, MAX(salary)
    FROM Employee
    GROUP BY departmentId
);
```
