Advantages of PL/SQL

1) Tight integrated with SQL

PL/SQL lets you use all SQL data manipulation, cursor control, and transaction control statements, 
and all SQL functions, operators, and pseudocolumns...

PL/SQL fully supports SQL data types - You need not convert between PL/SQL and SQL data types. 
For example, if your PL/SQL program retrieves a value from a column of the SQL type VARCHAR2, 
it can store that value in a PL/SQL variable of the type VARCHAR2.

You can give a PL/SQL data item the data type of a column or row of a database table without explicitly specifying that data type 
using %TYPE Attribute and %ROWTYPE Attribute.

PL/SQL supports both static and dynamic SQL. Static SQL is SQL whose full text is known at compile time. 
Dynamic SQL is SQL whose full text is not known until run time. Dynamic SQL lets you make your applications more flexible and versatile.

2) High Performance
 
PL/SQL lets you send a block of statements to the database, significantly reducing traffic between the application and the database.

Bind Variables
When you embed a SQL INSERT, UPDATE, DELETE, MERGE, or SELECT statement directly in your PL/SQL code, 
the PL/SQL compiler turns the variables in the WHERE and VALUES clauses into bind variables.
Oracle Database can reuse these SQL statements each time the same code runs, which improves performance.
PL/SQL does not create bind variables automatically when you use dynamic SQL, but you can use them with dynamic SQL by specifying them explicitly.

Optimizer
The PL/SQL compiler has an optimizer that can rearrange code for better performance.

Subprograms
PL/SQL subprograms are stored in executable form, which can be invoked repeatedly. Because stored subprograms run in the database server, 
a single invocation over the network can start a large job. This division of work reduces network traffic and improves response times. 
Stored subprograms are cached and shared among users, which lowers memory requirements and invocation overhead.

3) High Productivity

PL/SQL has many features that save designing and debugging time, and it is the same in all environments.
PL/SQL lets you write compact code for manipulating data. 
PL/SQL can query, transform, and update data in a database.

4) Portability

You can run PL/SQL applications on any operating system and platform where Oracle Database runs.

5) Scalability

PL/SQL stored subprograms increase scalability by centralizing application processing on the database server.
The shared memory facilities of the shared server let Oracle Database support thousands of concurrent users on a single node.

6) Manageability

PL/SQL stored subprograms increase manageability because you can maintain only one copy of a subprogram, on the database server, 
rather than one copy on each client system.
Any number of applications can use the subprograms, and you can change the subprograms without affecting the applications that invoke them.
