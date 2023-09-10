## 1) Create a function to convert dates to a given format.

````sql
CREATE OR REPLACE FUNCTION ConvertDateFormat(date_value date, "format" varchar2) RETURN varchar2
IS
    converted_date varchar2(255);
BEGIN
    converted_date := TO_CHAR(date_value, "format");
    RETURN converted_date;
EXCEPTION
    WHEN OTHERS THEN
        RAISE_APPLICATION_ERROR(-20004, 'Error while converting date');
END;
show errors
````
#### Test
````sql
SELECT first_name, ConvertDateFormat(hire_date, 'yyyy-dd-mm')
FROM employees;
````
![image](https://github.com/MohamedWageh09/PLSQL_Practising/assets/120044385/1e2a634f-d9ef-4e7d-bf8a-892fa0c68057)


## 2) Create and invoke the Query_loc Function to display the data for a certain region from Locations, Countries, regions tables in the following format" Region Name , Country Name , LOCATION_ID  , STREET_ADDRESS  , POSTAL_CODE , CITY" Pass Location ID as an input parameter

````sql
CREATE OR REPLACE FUNCTION query_loc(v_loc_id number)
    RETURN varchar2
    IS
    v_loc LOCATIONS%rowtype;
    v_country COUNTRIES%rowtype;
    v_region REGIONS%rowtype;
    v_result VARCHAR2(4000);
BEGIN
    SELECT LOCATION_ID,
            CITY,
            STREET_ADDRESS,
            POSTAL_CODE,
            COUNTRY_ID
    INTO v_loc.LOCATION_ID,
        v_loc.CITY,
        v_loc.STREET_ADDRESS,
        v_loc.POSTAL_CODE,
        v_loc.COUNTRY_ID
    FROM locations
    WHERE location_id = v_loc_id;

    SELECT COUNTRY_NAME,
          REGION_ID  
    INTO v_country.COUNTRY_NAME,
        v_country.REGION_ID
    FROM countries
    WHERE country_id = v_loc.COUNTRY_ID;
    
    SELECT REGION_NAME
    INTO v_region.REGION_NAME
    FROM regions
    WHERE region_id = v_country.REGION_ID;

    v_result := 'Location ID: '||v_loc.LOCATION_ID||' City: '||v_loc.CITY||' Street: '||v_loc.STREET_ADDRESS||' Postal'||v_loc.POSTAL_CODE||' Country: '||v_country.COUNTRY_NAME||' Region: '||v_region.REGION_NAME;
    RETURN v_result;
END;
````
#### Calling:
````sql
set serveroutput on size 1000000
DECLARE
v_test varchar2(255);
BEGIN
    v_test := query_loc(2400);
    dbms_output.put_line(v_test);

END;
````

## 3) Create and invoke the GET_LOC function to return the street address, city for a specified LOCATION_ID.

````sql
CREATE OR REPLACE FUNCTION get_loc(v_STREET_ADDRESS OUT varchar2,  v_CITY OUT varchar2, v_loc_id number)
    RETURN varchar2
    IS
    v_loc LOCATIONS%rowtype;
BEGIN
    SELECT LOCATION_ID,
            CITY,
            STREET_ADDRESS,
            POSTAL_CODE,
            COUNTRY_ID
    INTO v_loc.LOCATION_ID,
        v_loc.CITY,
        v_loc.STREET_ADDRESS,
        v_loc.POSTAL_CODE,
        v_loc.COUNTRY_ID
    FROM locations
    WHERE location_id = v_loc_id;
    v_STREET_ADDRESS := v_loc.STREET_ADDRESS;
    v_CITY := v_loc.CITY;
RETURN 'Done';
END;
````
#### Calling:
````sql
set serveroutput on size 1000000
DECLARE
v_loc_id number := 2400;
v_city varchar2(100);
v_street varchar2(100);
v_test varchar2(255);
BEGIN
    v_test := get_loc(v_street, v_city, v_loc_id);
    dbms_output.put_line('Street: '||v_street);
    dbms_output.put_line('City: '||v_city);
END;
````
## 4) Create a function called GET_ANNUAL_COMP to return the annual salary computed from an employee’s monthly salary and commission passed as parameters.  Use the following basic formula to calculate the annual salary:  (Salary * 12) + (commission_pct * salary * 12)
- Use the function in a SELECT statement
````sql
CREATE FUNCTION get_annual_comp(v_salary number, v_comm number)
RETURN number
IS
BEGIN
    RETURN (v_salary*12) + (v_comm*v_salary*12);
END;
show errors
````
#### Calling in SQL:
````sql
SELECT employee_id, get_annual_comp(salary, COALESCE(commission_pct, 0))
FROM employees;
````
![image](https://github.com/MohamedWageh09/PLSQL_Practising/assets/120044385/3097152c-2f4b-4fca-a071-59bcb5bee962)

#### NOTE: to call a created function in SQL you need to make sure that:
- function does not contain dmls( insert - update - delete ) on the same table .
- function does not contain plsql data types ( boolean - plsql records ).
- function does not contain out , in out parameter modes (returns more than 1 value).

## Create CHECK_RETIRED FUNCTION that takes employee_id and the retirement years as inputs (EMP_ID NUMBER, MAX_HIRE_YEAR NUMBER) 
- Add RETIRED NUMBER(1) column to employees table using alter
- Create and call CHECK_RETIRED FUNCTION(V_EMP_ID NUMBER, V_MAX_HIRE_YEAR NUMBER)
that will return 1 if employee has passed no of years >=  V_MAX_HIRE_YEAR, return 0 for otherwise
- create anonymous block to update the emp with set retired = 1  if this employee will retired

````sql
CREATE OR REPLACE FUNCTION check_retired(emp_id number, max_years number)
RETURN number
IS
    emp_record EMPLOYEES%rowtype;
    total_working_years number;
BEGIN
    SELECT hire_date
    INTO emp_record.hire_date
    FROM employees
    WHERE employee_id = emp_id;   
    total_working_years := TRUNC(TRUNC(MONTHS_BETWEEN(SYSDATE, emp_record.hire_date)) / 12);
    IF total_working_years >= max_years THEN
        RETURN 1;
    ELSE
        RETURN 0; 
    END IF;

END;
show errors
````

````sql
DECLARE
    v_count number;

BEGIN
    SELECT COUNT(column_name) INTO v_count FROM all_tab_columns WHERE column_name = 'RETIRED';
    IF v_count = 0 THEN
        EXECUTE IMMEDIATE 'ALTER TABLE EMPLOYEES ADD RETIRED number(1)';
    END IF;
END;

DECLARE
    CURSOR emp_cursor IS
        SELECT * FROM employees;
    max_years number := 18;
    v_retirement number;
BEGIN
    FOR emp_record IN emp_cursor LOOP
        SELECT check_retired(emp_record.employee_id, max_years)
            INTO v_retirement
            FROM EMPLOYEES
            WHERE employee_id = emp_record.employee_id;
    IF v_retirement = 1 THEN
        UPDATE employees 
        SET retired = 1
        WHERE employee_id = emp_record.employee_id; 
    END IF;   
    END LOOP; 
END;
````
![image](https://github.com/MohamedWageh09/PLSQL_Practising/assets/120044385/d6882ad4-f153-4072-b2fb-d7a2951aeb43)


