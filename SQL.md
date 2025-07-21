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
