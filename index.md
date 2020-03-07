# Learn about the Web

<hr>

### Day 1 - Relational Databases and SQL

1. A **database** is a collection of data. A **database system** is a system of storing  collections of data in some organised way.Databases maybe *relational* or *non-relational*.

2. **Features of DB-**
   - *Persistence:* Access data later after it was created.
   - *Concurrency control:* Allow multiple users of an application to read and write data at the same time.
   - *Ability to store data efficiently:* Allow multi types of data to be stored.
   - *Shared source of truth:* All users of an application can access the same data source.

3. **Relational databases**
   - All the data is stored in form of *tables*
   - Each table is organised into rows and columns; with each column having its own *data type* and each row containing *set of data*
   - It has rules for enforcing data integrity such as *contraints and triggers*.
   eg. PostgreSQL, MySQL, SQLite, Oracle, SQL Server.

4. **Primary and Foreign Keys**
   - Primary key is the unique identifier to uniquely identify an entire row of data. 
   - There might be multiple primary keys and the set of primary keys is called *composite key*.
   - Foreign key is an primary key from another table
   - Foreign keys establish relationship between tables.

5. **SQL**
   - Query language for relational databases.
   - Every relational DB has its own flavor called *dialect*(eg. PostgreSQL, MySQL, SQLite, Oracle, SQL Server) but is based on the SQL standard.

6. **Some SQL exercises**

   ```
   -- SQL Practice Exercises

   create table drivers (
   id serial primary key,
   first_name varchar,
   last_name varchar
   );

   create table vehicles (
   id serial primary key,
   make varchar,
   model varchar,
   driver_id integer references drivers(id)
   );

   -- Manipulating & Querying Data
   -- Insert a few records into both drivers and vehicles. Include 3 records of drivers  who have vehicles, belonging in the vehicles table.
   INSERT INTO drivers VALUES (1,'Amy','Hua');
   INSERT INTO drivers VALUES (2,'Mike','Chen');
   INSERT INTO drivers VALUES (3,'John','Smith');
   INSERT INTO drivers VALUES (4,'Jane','Doe');
   INSERT INTO drivers VALUES (5,'Vinod','Singh');

   INSERT INTO vehicles VALUES (1,'Honda','City',3);
   INSERT INTO vehicles VALUES (2,'Hyundai','Eon',1);
   INSERT INTO vehicles VALUES (3,'Suzuki','Eritga',5);
   INSERT INTO vehicles VALUES (5,'Nissan','Leaf',4);
   INSERT INTO vehicles VALUES (6,'Nissan','Micra',3);
   INSERT INTO vehicles VALUES (7,'Tata','Tiago',2);


   -- Select all driver records; select all vehicle records; select only 3 vehicle records (using LIMIT)
   -- driver records
   SELECT * 
   FROM drivers
   -- vehicle records
   SELECT *
   FROM vehicles
   -- limit records
   SELECT * FROM vehicles LIMIT 3

   -- Driver with ID 2 no longer owns any vehicles. Update the database to reflect this.
   DELETE
   FROM vehicles
   WHERE driver_id=2

   -- Driver with ID 1 now owns a new vehicle in addition to the previous one they owned. Update the database to reflect this.
   INSERT INTO vehicles VALUES (4,'Renault','Triber',1) 

   -- Joins & Group Bys
   -- Select all vehicles owned by driver with ID 3.
   SELECT * FROM vehicles
   WHERE driver_id=3

   -- Select all vehicles owned by driver with name 'Amy' (without knowing their ID).
   SELECT vehicles.make, vehicles.model FROM drivers
   INNER JOIN vehicles ON vehicles.driver_id=drivers.id AND drivers.first_name='Amy'

   -- Show a table of the number of vehicles owned per driver.
   SELECT vehicles.driver_id, COUNT(*) as num
   FROM vehicles
   group by vehicles.driver_id
   order by num desc

   -- Show the number of drivers that own a Nissan model.
   SELECT vehicles.make,COUNT(*) as num 
   FROM vehicles
   INNER JOIN drivers ON vehicles.make='Nissan' AND drivers.id=vehicles.driver_id
   group BY vehicles.make

   -- Structuring Data
   -- Add information about vehicle color.
   ALTER TABLE vehicles ADD COLUMN vehicle_color varchar 
   ```

7. **Execution plan**
   - Whenever we use an SQL query, the DBMS takes it and generates an *exection plan* for the DB engine to follow. 
   - Execution plan gives an idea of the *performance* of an SQL query since every step in a plan has a cost attached to it - higher the cost, more the execution time. 
   - Performance of a query directly affects the efficiency of interacting with a DB and hence while writing SQL queries we need to be aware of the performance trade offs.
   - Eg. for a simple *SELECT* query , the SQL application opens the file which has the table, runs a *Sequential scan* over the rows one by one and returns to the client.
   - Eg. for a *JOIN* query, the process is more complicated than a simple sequential scan. 
      A query like - 
         ```
         SELECT make, model from vehicles
         JOIN drivers on vehicles.driver_id = drivers.id;  
         ```
   has the following three step execution plan
      - Step 1: *Hash Join* - Most expensive step with highest cost. It is run to create a hash in memory and joins the two record sets going over every row.
    [Refer here](https://www.depesz.com/2013/05/09/explaining-the-unexplainable-part-3/#hash-join)
      - Step 2: *Sequential Scan or Seq Scan* - A sequential scan is run over the 'vehicles' table in the eg. since we need the model and make of the vehicles.
      - Step 3: *Hash with seq scan on 'drivers' record* - With the seq scan , the join key is checked in the Hash returned from Step 1 to verify if it exists. If it does not , the row is dropped and the next row is scanned. At the end , we are left with the rows which are found in the Hash generated in step 1. 

