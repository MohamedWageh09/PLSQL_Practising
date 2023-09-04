

## Q1) Create plsql block  to check for all employees using cursor and update their commission_pct based on the salary:
```
SALARY < 7000  COMM = 0.1
7000 <= SALARY < 10000     COMM = 0.15
10000 <= SALARY < 15000    COMM = 0.2
15000 <= SALARY  	COMM = 0.25
 ```
```sql
DECLARE
    CURSOR employee_cursor IS 
        SELECT * FROM employees;
        
v_comm_pct Employees.COMMISSION_PCT%type;
BEGIN
    FOR emp_record IN employee_cursor LOOP
        IF emp_record.SALARY < 7000 THEN
            v_comm_pct := 0.1;
        ELSIF emp_record.SALARY >= 7000 AND emp_record.SALARY < 10000 THEN
            v_comm_pct := 0.15;
        ELSIF emp_record.SALARY >= 1000 AND emp_record.SALARY < 15000 THEN
            v_comm_pct := 0.2;
        ELSIF emp_record.SALARY >= 15000 THEN
            v_comm_pct := 0.25;
        END IF;
    UPDATE employees SET COMMISSION_PCT = v_comm_pct WHERE employee_id = emp_record.employee_id;
    END LOOP;
END;
```

## Q2) Alter table employees then add column retired_bonus then create plsql block to calculate the retired salary for all employees using cursor and update retired_bonus column only for those employees have passed 18 years of their hired date.
```
Retired bonus = no of working months * 10 % of his current salary
```

```sql
DECLARE
v_count NUMBER(4);
BEGIN

SELECT COUNT(*) INTO v_count FROM ALL_TAB_COLUMNS  WHERE TABLE_NAME= 'EMPLOYEES' AND COLUMN_NAME = 'RETIRED_BONUS';
IF v_count = 0 THEN
    EXECUTE IMMEDIATE 'ALTER TABLE EMPLOYEES ADD RETIRED_BONUS NUMBER(8, 2)';
    COMMIT;
END IF;
END;

DECLARE
v_retired_salary NUMBER(8,2);
CURSOR emp_cursor IS 
    SELECT * FROM employees WHERE employee_id IN (SELECT employee_id FROM employees WHERE TRUNC(MONTHS_BETWEEN(sysdate, hire_date)) > 18);
BEGIN
FOR emp_record IN emp_cursor LOOP
        v_retired_salary := TRUNC(MONTHS_BETWEEN(sysdate, emp_record.hire_date)) * (emp_record.salary * 0.1);
        UPDATE employees SET RETIRED_BONUS = v_retired_salary WHERE employee_id = emp_record.employee_id;    
END LOOP;

END;
```

## Q3) Create plsql block using cursor to print last name, department name, city, country name for all employees employee ( without using join | sub query ).

```sql
set serveroutput on size 1000000
DECLARE
    CURSOR emp_cursor IS
        SELECT * FROM employees WHERE department_id IS NOT NULL;
v_department_name DEPARTMENTS.department_name%type;
v_location_id LOCATIONS.location_id%type;
v_city LOCATIONS.city%type;
v_country_id COUNTRIES.country_id%type;
v_country_name COUNTRIES.country_name%type;
BEGIN
    FOR employee_record IN emp_cursor LOOP
        SELECT department_name, location_id INTO v_department_name, v_location_id FROM departments WHERE department_id = employee_record.department_id;
        SELECT city, country_id INTO v_city, v_country_id FROM locations WHERE location_id = v_location_id;
        SELECT country_name INTO v_country_name FROM countries WHERE country_id = v_country_id;
        dbms_output.put_line(employee_record.last_name||' '|| v_department_name||' '||v_city||' '||v_country_name);
    END LOOP;
END;
```

## Q4)	Create plsql block that loop over employees table and increase only those working in ‘IT’ department by 10% of their salary. 

```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT * FROM employees WHERE department_id = (SELECT department_id from departments WHERE department_name = 'IT');
BEGIN
    FOR emp_record IN emp_cursor LOOP
        UPDATE employees SET salary = salary + (salary * 0.1) WHERE employee_id = emp_record.employee_id;  
    END LOOP;
END;
```

## Q5) Create empty table employees_again2 and use Cursor loop to insert only employees whose job_id = ‘SA_REP’ to the new table with double salary [ salary * 2 ].

```sql
DECLARE
    v_count NUMBER(2);
BEGIN
    SELECT COUNT(*) INTO v_count FROM ALL_TAB_COLUMNS WHERE table_name = 'EMPLOYEES_AGAIN2';
    IF v_count = 0 THEN
        EXECUTE IMMEDIATE 'CREATE TABLE employees_again2 AS SELECT * FROM employees WHERE 2=1';     
    END IF;
END;

DECLARE
    CURSOR emp_cursor IS
        SELECT * FROM employees WHERE job_id = 'SA_REP';
BEGIN
    FOR emp_record IN emp_cursor LOOP
        INSERT INTO employees_again2(EMPLOYEE_ID, FIRST_NAME, LAST_NAME, EMAIL, PHONE_NUMBER, HIRE_DATE, JOB_ID, SALARY, COMMISSION_PCT, MANAGER_ID, DEPARTMENT_ID)
            VALUES(emp_record.EMPLOYEE_ID, emp_record.FIRST_NAME, emp_record.LAST_NAME, emp_record.EMAIL, emp_record.PHONE_NUMBER, emp_record.HIRE_DATE, emp_record.JOB_ID, emp_record.SALARY * 2, emp_record.COMMISSION_PCT, emp_record.MANAGER_ID, emp_record.DEPARTMENT_ID);
    END LOOP;
END;
```

## Q6) Using cursor loop to loop over departments and print dept_id, dept_name.

```sql
DECLARE
CURSOR dept_cursor IS
    SELECT * FROM departments WHERE department_id IS NOT NULL;
BEGIN
    FOR dept_record IN dept_cursor LOOP
        dbms_output.put_line('department id: '||dept_record.department_id||' '||'department name: '||dept_Record.department_name);
     
    END LOOP;
END;
```

## Q7) Create plsql block that insert new Departments with the following data:
```
Department_id = 350
Department name = Oracle Dept
Manager id = 103
Location Id = 17

** Handle exception as needed
```

```sql
set serveroutput on size 1000000
DECLARE
    key_violated exception;
    pragma exception_init(key_violated, -02291);
BEGIN
    INSERT INTO departments(DEPARTMENT_ID, DEPARTMENT_NAME, LOCATION_ID, MANAGER_ID)
        VALUES(350, 'Oracle Dept', 17, 103);
        
    EXCEPTION
        WHEN key_violated THEN
            dbms_output.put_line('foregin key violated');
        WHEN others THEN
            dbms_output.put_line('please check the script');
END;
```
