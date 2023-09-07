## Procedure to give a bonus to employees:
````sql
CREATE PROCEDURE employee_bonus(v_emp_id number, v_bonus_perc number)
    IS
    v_salary EMPLOYEES.salary%type;
    v_salary_after_bonus EMPLOYEES.salary%type;
BEGIN
    SELECT salary
    INTO v_salary
    FROM employees 
    WHERE employee_id = v_emp_id;
    v_salary_after_bonus := v_salary + v_salary * v_bonus_perc /100;
    UPDATE employees
    SET salary = v_salary_after_bonus
    WHERE employee_id = v_emp_id;

END;

show errors
````
#### Calling it for specific departments:
** the 2 select statements before and after the block are for checking and validating.
````sql
SELECT * FROM employees WHERE department_id IN(40, 60, 110, 20);
DECLARE
    CURSOR emp_cursor IS 
        SELECT * FROM employees;
BEGIN
    FOR emp_record IN emp_cursor LOOP
        IF emp_record.department_id = 40 THEN
            employee_bonus(emp_record.employee_id, 5);
        ELSIF emp_record.department_id = 60 THEN
            employee_bonus(emp_record.employee_id, 20);
        ELSIF emp_record.department_id = 110 THEN
            employee_bonus(emp_record.employee_id, 10);
        ELSIF emp_record.department_id = 20 THEN
            employee_bonus(emp_record.employee_id, 5);
        END IF;
    END LOOP;
END;
SELECT * FROM employees WHERE department_id IN(40, 60, 110, 20);

````

## Procedure to give commissions to employees, each employee monthly commission is stored in the same database. After giving the commission set it to null to be ready for next month's commission
````sql
CREATE OR REPLACE PROCEDURE give_commission(v_emp_id number, v_commission number)
    IS
 v_salary EMPLOYEES.SALARY%type; 
 v_next_month_sal  EMPLOYEES.SALARY%type;
BEGIN
    SELECT
    salary
    INTO v_salary
    FROM employees
    WHERE employee_id = v_emp_id;
    
    v_next_month_sal := v_salary + v_salary * v_commission;
    UPDATE employees
    SET salary = v_next_month_sal
    WHERE employee_id = v_emp_id;
    
    UPDATE employees
    SET commission_pct = NULL
    WHERE employee_id = v_emp_id;
END;

show errors
````
#### Calling it:
````sql
DECLARE
    CURSOR emp_cursor IS
        SELECT * FROM employees;
BEGIN
    FOR emp_record IN emp_cursor LOOP
        give_commission(emp_record.employee_id, emp_record.commission_pct);
    END LOOP;
END;
````
