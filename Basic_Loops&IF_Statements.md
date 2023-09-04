# For i loop & IFs
I used HR schema & toad
## Q1) Create plsql block and to check for employee with id = 105, Then update his commission_pct based on his salary:
```
SALARY < 7000   COMM = 0.1
7000 <= SALARY < 10000   COMM = 0.15
10000 <= SALARY < 15000   COMM = 0.2
15000 <= SALARY  	COMM = 0.25
```
Solution:

```sql
DECLARE
    v_id EMPLOYEES.EMPLOYEE_ID%type := 105;
    v_comm EMPLOYEES.COMMISSION_PCT%type;
    v_salary EMPLOYEES.SALARY%type;
BEGIN
    SELECT SALARY
    INTO v_salary
    FROM Employees
    WHERE Employee_id = v_id;
    
    IF v_salary < 7000 then
        v_comm := 0.1;
    ELSIF v_salary >= 7000 AND v_salary < 1000 then
        v_comm := 0.15;
    ELSIF v_salary >= 10000 AND v_salary < 15000 then
        v_comm := 0.2;
    ELSIF v_salary >= 15000 then
        v_comm :=0.25;
    END IF;
    
    UPDATE Employees SET COMMISSION_PCT = v_comm WHERE employee_id = v_id;
END;
```

## Q2) Create plsql block to calculate the retired salary for the employee no = 105
    - Retired salary = no of working months * 10 % of his current salary

```sql
DECLARE
    v_working_months NUMBER(5);
    v_hire_date DATE;
    v_salary EMPLOYEES.SALARY%type;
    v_retired_sal EMPLOYEES.SALARY%type;
    v_min NUMBER(4);
    v_max NUMBER(4);
    v_count NUMBER(4);
BEGIN
    SELECT MIN(employee_id), MAX(employee_id) 
    INTO v_min, v_max 
    FROM employees;
    FOR i in v_min..v_max LOOP
    SELECT COUNT (*) INTO v_count FROM Employees WHERE employee_id = i;
    IF v_count = 1 THEN
        SELECT salary, hire_date
        INTO v_salary, v_hire_date
        FROM Employees
        WHERE employee_id = i;
        v_working_months := TRUNC(MONTHS_BETWEEN(SYSDATE, v_hire_date));
        v_retired_sal := v_working_months * (v_salary * 0.1);
        dbms_output.put_line(v_retired_sal);
    END IF;
    END LOOP;
END;
```

## Q3) Create plsql block to print last name, department name, city, country name for employee whose id = 105 ( without using join | sub query )

```sql
DECLARE
    v_id EMPLOYEES.EMPLOYEE_ID%type := 105;
    v_last_name EMPLOYEES.LAST_NAME%type;
    v_department_id DEPARTMENTS.DEPARTMENT_ID%type;
    v_dept_name DEPARTMENTS.DEPARTMENT_NAME%type;
    v_location_id LOCATIONS.LOCATION_ID%type;
    v_country_id COUNTRIES.COUNTRY_ID%type;
    v_country_name COUNTRIES.COUNTRY_NAME%type;
    v_city LOCATIONS.CITY%type;
BEGIN
    SELECT last_name, DEPARTMENT_ID
    INTO v_last_name, v_department_id
    FROM employees
    WHERE employee_id = v_id;
    
    SELECT DEPARTMENT_NAME, location_id
    INTO v_dept_name, v_location_id
    FROM Departments
    WHERE department_id = v_department_id;
    
    SELECT city, country_id
    INTO v_city, v_country_id
    FROM locations
    WHERE location_id = v_location_id;
    
    SELECT country_name
    INTO v_country_name
    FROM countries
    WHERE country_id = v_country_id;
    
    dbms_output.put_line(v_last_name||' '||v_dept_name||' '||v_city||' '||v_country_name);
END;
```

## Q4) 4.	Create plsql block that loop over employees table and increase only those working in ‘IT’ department by 10% of their salary
```sql
DECLARE
    v_min NUMBER(4);
    v_max NUMBER(4);
    v_count NUMBER(4);
    v_salary NUMBER(8, 2);
    v_dept_id NUMBER(4);
BEGIN
SELECT MIN(employee_id), MAX(employee_id)
INTO v_min, v_max
FROM employees;
FOR i in v_min..v_max LOOP
    SELECT COUNT(*) INTO v_count FROM employees WHERE employee_id = i;
    IF v_count = 1 THEN
        SELECT salary, department_id
        INTO v_salary, v_dept_id
        FROM employees
        WHERE employee_id = i;
        
        UPDATE employees SET salary = v_salary + v_salary * 0.1 WHERE department_id = v_dept_id AND employee_id = i;
    END IF;
    END LOOP;
END;
```

## Q5) Create empty table employees_again and use loop to insert only employees whose job_id = ‘SA_REP’ to the new table with double salary [ salary * 2 ]
```sql
DECLARE
    v_count NUMBER(5);
    v_counter NUMBER(5);
    v_min NUMBER(4);
    v_max NUMBER(4);
     v_salary EMPLOYEES.SALARY%type;
    v_phone_number EMPLOYEES.PHONE_NUMBER%type;
    v_manager_id EMPLOYEES.MANAGER_ID%type;
    v_first_name EMPLOYEES.FIRST_NAME%type;
    v_last_name EMPLOYEES.LAST_NAME%type;
    v_job_id EMPLOYEES.JOB_ID%type;
    v_hire_date EMPLOYEES.HIRE_DATE%type;
    v_employee_id EMPLOYEES.EMPLOYEE_ID%type;
    v_email EMPLOYEES.EMAIL%type;
    v_department_id DEPARTMENTS.DEPARTMENT_ID%type := 80;
    v_comm EMPLOYEES.COMMISSION_PCT%type;
BEGIN
   SELECT COUNT(*) INTO v_count FROM user_tables WHERE table_name = 'EMPLOYEES_AGAIN';
   IF v_count = 1 THEN
      EXECUTE IMMEDIATE 'TRUNCATE TABLE employees_again';
   ELSE
      EXECUTE IMMEDIATE 'CREATE TABLE employees_again (EMPLOYEE_ID NUMBER(6), FIRST_NAME VARCHAR2(20 BYTE), LAST_NAME VARCHAR2(25 BYTE) CONSTRAINT EMP_LAST_NAME_NN NOT NULL, EMAIL VARCHAR2(25 BYTE) CONSTRAINT EMP_EMAIL_NN NOT NULL, PHONE_NUMBER VARCHAR2(20 BYTE), HIRE_DATE DATE CONSTRAINT EMP_HIRE_DATE_NN NOT NULL, JOB_ID VARCHAR2(10 BYTE) CONSTRAINT EMP_JOB_NN NOT NULL, SALARY NUMBER(8,2), COMMISSION_PCT NUMBER(2,2), MANAGER_ID NUMBER(6), DEPARTMENT_ID NUMBER(4))';
   END IF;

SELECT MIN(employee_id), MAX(employee_id) INTO v_min, v_max FROM employees WHERE job_id = 'SA_REP';
    FOR i IN v_min..v_max LOOP
    SELECT COUNT(*) INTO v_counter FROM employees WHERE employee_id = i;
    IF v_counter = 1 THEN
        SELECT SALARY, PHONE_NUMBER, MANAGER_ID, LAST_NAME, FIRST_NAME, JOB_ID, HIRE_DATE,  EMPLOYEE_ID, EMAIL, DEPARTMENT_ID, COMMISSION_PCT
        INTO v_salary, v_phone_number, v_manager_id, v_last_name, v_first_name, v_job_id, v_hire_date, v_employee_id, v_email, v_department_id, v_comm
        FROM Employees
        WHERE employee_id = i;
        
        INSERT INTO employees_again(EMPLOYEE_ID, FIRST_NAME, LAST_NAME, HIRE_DATE, JOB_ID, MANAGER_ID, PHONE_NUMBER, SALARY, COMMISSION_PCT, DEPARTMENT_ID, EMAIL)
        VALUES(v_employee_id, v_first_name, v_last_name, v_hire_date, v_job_id, v_manager_id, v_phone_number, v_salary, v_comm, v_department_id, v_email);
    END IF;   
    END LOOP;
EXECUTE IMMEDIATE 'COMMIT';
END;
```

## Q6) Use loop to loop over departments and print dept_id, dept_name
```sql
DECLARE
    v_min NUMBER(4);
    v_max NUMBER(4);
    v_dept_id NUMBER(4);
    v_dept_name VARCHAR2(100);
    v_counter NUMBER(4);
BEGIN
SELECT MIN(department_id), MAX(department_id)
INTO v_min, v_max
FROM departments;
FOR i in v_min .. v_max LOOP
SELECT COUNT(*) INTO v_counter FROM departments WHERE department_id = i;
IF v_counter = 1 THEN
    SELECT department_id, department_name
    INTO v_dept_id, v_dept_name
    FROM DEPARTMENTS
    WHERE department_id = i;
    dbms_output.put_line(v_dept_id||' '||v_dept_name);
END IF;
END LOOP;
END;
```


