# 🏢 A Company Breakdown (Male and Female Group)

First, we select a specific database in a database management system using `USE` code
```sql
USE employees;
```

### 📌Visualize the yearly breakdown of male and female employees in the company since 1990
```sql
SELECT 
    YEAR(d.from_date) AS calendar_year,
    e.gender,
    COUNT(e.emp_no) AS num_of_employees
FROM
    t_employees e
JOIN
    t_dept_emp d ON d.emp_no = e.emp_no
GROUP BY calendar_year, e.gender
HAVING calendar_year >= 1990;
```

### 📌Analyze male and female managers across departments each year, starting from 1990
```sql
SELECT 
    dm.emp_no,
    ee.gender,
    d.dept_name,
    e.calendar_year,
    dm.from_date,
    dm.to_date,
    CASE
        WHEN YEAR(dm.to_date) >= e.calendar_year AND YEAR(dm.from_date) <= e.calendar_year THEN 1 ELSE 0
    END AS active
FROM
    (SELECT 
        YEAR(hire_date) AS calendar_year
    FROM
        t_employees
    GROUP BY calendar_year) e
        CROSS JOIN
    t_dept_manager dm
        JOIN
    t_departments d ON dm.dept_no = d.dept_no
       JOIN 
    t_employees ee ON dm.emp_no = ee.emp_no
ORDER BY dm.emp_no, calendar_year;
```

### 📌Compare average salaries for male and female employees up to 2002, with department-specific filtering
```sql
SELECT 
    e.gender,
    d.dept_name,
    ROUND(AVG(s.salary), 2) AS salary,
    YEAR(s.from_date) AS calendar_year
FROM
    t_salaries s
        JOIN
    t_employees e ON s.emp_no = e.emp_no
        JOIN
    t_dept_emp de ON de.emp_no = e.emp_no
        JOIN
    t_departments d ON d.dept_no = de.dept_no
GROUP BY d.dept_no , e.gender , calendar_year
HAVING calendar_year <= 2002
ORDER BY d.dept_no;
```

### 📌Design an SQL stored procedure for average gender-based salaries per department within user-defined salary ranges
```sql
DROP PROCEDURE IF EXISTS filter_salary;

DELIMITER $$
CREATE PROCEDURE filter_salary (IN p_min_salary FLOAT, IN p_max_salary FLOAT)
BEGIN
SELECT 
    e.gender, d.dept_name, AVG(s.salary) as avg_salary
FROM
    t_salaries s
        JOIN
    t_employees e ON s.emp_no = e.emp_no
        JOIN
    t_dept_emp de ON de.emp_no = e.emp_no
        JOIN
    t_departments d ON d.dept_no = de.dept_no
    WHERE s.salary BETWEEN p_min_salary AND p_max_salary
GROUP BY d.dept_no, e.gender;
END$$

DELIMITER ;

CALL filter_salary(50000, 90000);
```

### 📊 Visualize the obtained result-set in Tableau.
Produced a 1-pager dashboard using Tableau. (Interactive Dashboard: [Link](https://public.tableau.com/app/profile/ramadhani.alifa/viz/Exercise2-ACompanyBreakdownMaleandFemaleGroup/Dashboard1))

![Dashboard 1](https://github.com/punyarani/exercise/assets/99372162/830252ca-b710-4b57-89ca-590be4c60266)

