

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

## Q2) Alter table employees then add column retired_bonus then create plsql block to calculate the retired salary for all employees using cursor and update retired_bonus column only for those employees have passed 18 years of their hired date
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

