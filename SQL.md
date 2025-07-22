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

### How do you improve read transaction from db apart from creating indexs

✅ 1. Use Proper Indexing  
Use B-tree indexes for exact lookups and range scans  

Use composite indexes when filtering on multiple columns  

Covering indexes can satisfy the query entirely  
```
-- Create index on frequently queried column
CREATE INDEX idx_employee_dept ON employee(department_id);
```
⚠️ Avoid over-indexing — it can hurt write performance.

✅ 2. Caching (In-Memory or External)  
Reduce DB hits by storing frequently accessed data.  

In-memory caches: ConcurrentHashMap, Guava Cache  

External caches: Redis, Memcached  

Spring Boot: Use @Cacheable  
```
@Cacheable("employee")
public Employee getEmployeeById(Long id) {
    return employeeRepository.findById(id).orElse(null);
}
```
✅ 3. Read Replicas (Database-Level)  
Use read replicas in systems like MySQL, PostgreSQL, or Aurora.  

Offload read traffic from the primary DB  

Useful in high-read, low-write scenarios  

Configure your app to route reads to replicas  

✅ 4. Connection Pooling  
Avoid creating a new connection per request.  

Use HikariCP, Apache DBCP, or C3P0  

Proper pool sizing avoids latency due to connection churn  

# Example in Spring Boot
```
spring.datasource.hikari.maximum-pool-size=20
```

✅ 5. Optimized Queries  
Avoid SELECT *, select only necessary columns  

Use LIMIT, OFFSET for pagination  

Rewrite N+1 queries with JOINs or batch fetching  
```
-- Instead of:
SELECT * FROM employees;

-- Do:
SELECT id, name, department FROM employees LIMIT 100;
```
✅ 6. Materialized Views  
Precompute and store query results for expensive aggregations or joins.  

Best for analytical queries  

Need periodic refresh  
```
CREATE MATERIALIZED VIEW top_salaries AS
SELECT department_id, MAX(salary) FROM employees GROUP BY department_id;
```
✅ 7. Partitioning / Sharding  
Divide large tables into smaller ones:  

Horizontal partitioning (e.g., based on region, date)  

Improves I/O and query performance  

✅ 8. Denormalization  
Store read-optimized views or copies of data to avoid expensive joins.  

Use with caution (redundancy risk)  

Best when reads vastly outnumber writes  
