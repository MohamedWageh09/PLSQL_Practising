## 1) Create and invoke the Query_loc Function to display the data for a certain region from Locations, Countries, regions tables in the following format" Region Name , Country Name , LOCATION_ID  , STREET_ADDRESS  , POSTAL_CODE , CITY" Pass Location ID as an input parameter

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

## 2) Create and invoke the GET_LOC function to return the street address, city for a specified LOCATION_ID.

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
## 3) Create a function called GET_ANNUAL_COMP to return the annual salary computed from an employee’s monthly salary and commission passed as parameters.  Use the following basic formula to calculate the annual salary:  (Salary*12) + (commission_pct*salary*12)
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
#### Calling:
````sql
SELECT employee_id, get_annual_comp(salary, COALESCE(commission_pct, 0))
FROM employees;
````
