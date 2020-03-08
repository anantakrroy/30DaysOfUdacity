# Learn about the Web

<hr>

![Hits](https://hitcounter.pythonanywhere.com/count/tag.svg?url=https%3A%2F%2Fanantakrroy.github.io%2F30DaysOfUdacity%2F)



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


### Day 2 : Client-Server Model, ACID in DBMS and DBAPIs

1. **Client-Server model**
   - *Server* --> is a centralized program that communicates over a network to *serve* the clients.
   - *Client* --> is a program that can request data from the server
   - *Host* --> is a computer connected over the client. Servers and clients run on hosts.
   - Relational databases follow the client-server model.
   - *Request and response* --> A client sends a request to the server for data; the job of the server is to process the request and send it back to the client.
   Request and response occurs over a *communication protocol* which decides the rules for such a communcation between the client and server!
   - **! IMPORTANT --** A *database client* is any program that sends a request to the database.So, a web server can also serve as a client to a DB if it sends a request to the DB. 
   - An example using an ecommerce site
      - User clicks a 'product' on a page.
      - The click event is registered by the users browser.
      - The click 'handler function' sends a request to the server.
      - The server listens to the request, processes the request , sends request to the DB server for data.
      - The DB processes the request and sends a response to the web server.
      - The web server receives the data, decides on the view to be rendered and provides the data to be used in the view.
      - The view template is populated with the data and sent back to the client.
      - The view is rendered on the client side with the data as well as the representation of the view.

2. **Communication protocols - TCP and UDP**
   - TCP/IP is one of the communicatio protocols used by client and server to communicate with each other. It is used to communicate between devices and transfer data over the internet.
   - Communication uses:
      - IP address --> to locate the particular device on a network
      - Port --> location on the recepient computer where the data is received.
      - Port 5432 is used as default for Postgres.
   - TCP/IP is a **connection based** protocol. All communication between parties is established over a connection. So a connection is established anytime the client and server need to communicate. 
   - Connecting *starts* and session. Ending the connection *stops* the session.
   - In a *database session*, many **transactions** can occur over a single session. Transactions are atomic pieces of work for the DB to perform as a whole. The sessions commit work to the database.
   - **! IMPORTANT --** Work in a database server is bundled into *Transactions*. The transactions follow the **ACID** principle
      - *A for Atomicity* --> Entire transaction takes place at once or doesnt happen at all
      - *C for Consistency* --> DB must be consistent before and after the transaction
      - *I for Isolation* --> Multiple transactions occur without interference.
      - *D for durability* --> Changes of a successful transaction occurs even if the system failure occurs. 
      Work in a DB is bundled into atomic transactions to ensure if any part of the transaction fails, the entire transaction can be *rolled back* to ensure consistncy of the DB. Also, every transaction if successful commits work to the DB .
   - *Transactions* capture operations that change a DB's data like updates, deletion, addition etc. They **are not** concerned with the querying aspects like grouping , selecting or ordering . 
   - A transaction can capture one or more pieces of work into a bundle executed in order.
   - **UDP protocol** --> The hosts on the network send data over the network in units called datagrams without any connection being established.Although,TCP is much more reliant way of sending data, it involves a lot of overhead and is slower. UDP is used when speed is more important than reliability like streaming services or VoIP. UDP does not need to re transmit lost datagrams unlike TCP where if a transaction fails it is re-transmitted back to the server.

**3. psql**
   - psql is an interactive terminal application for connecting and interacting with local Postgres server.
   - Default owner of db is "postgres"
   - In Windows , to get started with psql
      
      ``` psql -U <username>```

      For example:  

      ``` psql -U postgres```
   - `psql` has several meta commands to interact with the postgres server
   - `\l` --> Lists all the databases in the server with additional info like owners, access privileges.
   - `\c <dbname>` --> Connect to a db with name `dbname`
   - `\dt` --> Show all the tables in the current connected db.
   - `\d <tablename>` --> Show the schema of table with name `tablename` with info about *nullable* property.
   - `\q` --> quit psql and return to terminal/command prompt.
    



