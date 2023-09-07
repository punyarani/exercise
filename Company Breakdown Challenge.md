# 🏢 A Company Breakdown (Male and Female Group)

First, we select a specific database in a database management system using `USE` code
```sql
USE employees;
```
### 📌Visualize the yearly breakdown of male and female employees in the company since 1990
   - Extracts the calendar year and employee gender, also counts the number of employees.  <br>
   - Joins employee and department data.  <br>
   - Groups and filters data based on the need.  <br>
   - To make it easier to read, I sorted the data for years from 1990 onwards.
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
HAVING calendar_year >= 1990
ORDER BY calendar_year;
```
Output: <br>
![Untitled](https://github.com/punyarani/exercise/assets/99372162/e17caa2d-3f40-483d-969f-7e550f8a7602)


### 📌Analyze male and female managers across departments each year, starting from 1990
- A `CASE` statement determines if a manager was active in a given calendar year based on their from_date and to_date values. If they were active, it assigns a value of 1; otherwise, it assigns 0.  <br>

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
Output: <br>
![Untitled](https://github.com/punyarani/exercise/assets/99372162/f260552c-e022-4c20-b85f-b428a391bf45)


### 📌Compare average salaries for male and female employees up to 2002, with department-specific filtering
- The data is grouped by department number, employee gender, and calendar year. This allows for the calculation of average salaries within each department, gender, and year.
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
Output: <br>
![Untitled](https://github.com/punyarani/exercise/assets/99372162/77aa554f-7fa9-4be6-a394-4a9579b01a77)

### 📌Design an SQL stored procedure for average gender-based salaries per department within user-defined salary ranges
- Here, stored procedure is used so that we can call it with the desired salary range, and it will return the average salaries within that range for each department and gender combination.
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
GROUP BY d.dept_no, e.gender
ORDER BY d.dept_no;
END$$

DELIMITER ;
```
We can execute the `filter_salary` procedure by using the CALL statement, providing the minimum and maximum salary values as parameters, like this
``` sql
CALL filter_salary(50000, 90000);
```
Output: <br>
![Untitled](https://github.com/punyarani/exercise/assets/99372162/5fed31ae-7f2f-4354-8b03-fb25e0bcbf09)


### 📊 Visualize the obtained result-set in Tableau.
Produced a 1-pager dashboard using Tableau. (Interactive Dashboard: [Link](https://public.tableau.com/app/profile/ramadhani.alifa/viz/Exercise2-ACompanyBreakdownMaleandFemaleGroup/Dashboard1))

![Dashboard 1](https://github.com/punyarani/exercise/assets/99372162/830252ca-b710-4b57-89ca-590be4c60266)

