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


### Day 2 : Client-Server Model, ACID in DBMS

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
   - Default owner/username of db is "postgres"
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
   - *Shortcut to start a connection to db **drivers** with username **postgres** would be ``` psql drivers postgres```*

### Day 3. DBAPIs

   **Definition**
   - A DBAPI or Database Adapter provides an interface for a programming language to interact with a relational DB server
   - It is a *low level library* which allows to directly write SQL statements that connect to a DB
   - DBAPIs serve different server frameworks i.e language + DB system. eg. -
      - Ruby ---- pg
      - Node ---- node-postgres
      - Python(like Django, Flask) ---- psycopg2
   - These DBAPIs define standards for using the *result of DB queries* as *inputs to a given language*. eg. -> the result of `SELECT * from mydemodb` DB query which are rows of the DB `mydemodb` can be used as a JS array of objects using a NodeJS adapter like *nodejs-postgres* or as a set of tuples using a Python DB adapter like *psycopg2*.

**psycopg2**
   - **Basic usage:**  
      - need to import the installed *psycopg2* package

         ``` import psycopg2```
      - Setup a connection to an existing database which means we are starting a session and it begins a transaction(recall A of ACID for DBMS)
      
         ``` connection = psycopg2.connect("dbname=<yourdbname> user=<username | postgres> password=*******")```
      - Open a cursor to perform DB operations

         ```cursor = connection.cursor()```

         *what is a cursor????? A database cursor is a control structure that enables traversal over the records in a database. Cursors facilitate subsequent processing in conjunction with the traversal, such as retrieval, addition and removal of database records.*
      - Execute SQL queries. Eg.--

         ```
         cursor.execute('''
               CREATE TABLE todo(
                  id serial PRIMARY KEY,
                  description varchar NOT NULL
               );
            ''')
         ```
         ```
         cursor.execute("INSERT INTO todo(id, description) VALUES (1, 'buy 2L milk');")
         ```
      - Commit the changes to make the db persistent

         ```connection.commit()```
      - Close the connection with the db

         ```connection.close()```

         ```cursor.close()``` 
   
   - **String composition**
      Can use either eg. %s or named variables to make the code cleaner and more readable
      eg. 
      - Using %s

      ```cursor.execute('INSERT INTO trips(id, status, number_of_trips) VALUES (%s, %s, %s);', (1, True, 4))``` 
      - Using named variables
      
      ```cursor.execute('INSERT INTO trips(id, status, number_of_trips) VALUES +  (%(id)s, %(status)s, %(number_of_trips)s);', {'id' : 2, 'status' : False, 'number_of_trips' : 4}```

      OR much better to exploit the power of variables while using named variables

      ```
      SQL = 'INSERT INTO trips(id, status, number_of_trips) VALUES +  (%(id)s, %(status)s, %(number_of_trips)s);'
      data = {'id' : 2, 'status' : False, 'number_of_trips' : 4}
      cursor.execute(SQL, data)
      ```
   - **Example code**
   
      ```
      import psycopg2

      connection = psycopg2.connect('dbname=todoapp user=postgres password=3120358')

      cursor = connection.cursor()

      # drop existing table
      cursor.execute('DROP TABLE IF EXISTS todo;')

      cursor.execute("""
         CREATE TABLE todo(
            id serial PRIMARY KEY,
            description VARCHAR NOT NULL
         );
      """)

      # execute SQL queries using the cursor
      cursor.execute("INSERT INTO todo(id, description) VALUES (1, 'Buy Milk');")

      # using named variables
      SQL = 'INSERT INTO todo(id, description) VALUES (%(id)s, %(description)s);'
      data = {'id' : 2, 'description' : 'Buy bread'}
      cursor.execute(SQL, data)

      # get the results
      cursor.execute('SELECT * from todo;')
      result1 = cursor.fetchone()
      print(result1) 

      # insert one more after fetching results
      cursor.execute("INSERT INTO todo(id, description) VALUES (3, 'Buy veggies');")
      cursor.execute('SELECT * from todo;')

      // Need to execute cursor before fetching again
      resultAll = cursor.fetchall()
      print(resultAll)

      // Print the todo items from the db 
      for record in range(len(resultAll)) :
         print('Record :', resultAll[record][0] , 'contains', resultAll[record][1])

      connection.commit()

      connection.close()
      cursor.close()
      
      ```


### Day 4. ORMs and SQLAlchemy
   - ORM or Object Relational Mapping is one of the ways of adding in a layer of abstraction to a web application when interacting with a DB. DB libraries like *psycopg2* help us to directly interact with a DB but writing SQL queries is prone to mistakes when we are working on the server side in a different framework like python which has its own syntax.
   - ORM is the highest level of abstraction while interacting with a DB in a web app. 
   - ORMs help to use object oriented programming to interact with a DB.
   - ORMs help to map tables and columns to objects and attributes.
   - **SQLAlchemy**
      - Allows SQL queries to be written in form of python functions
      - Helps generate both SQL and python code --- lesser lines of code
      - Avoid sending SQL queries to DB everytime(how does this help???)
      - It would work with any DBMS . On the other hand, each SQL query language has a flavor of its own and would work on only limited number of machines running the particular flavor or SQL. It also gives us the flexibility of switch DB systems without rewriting code. 

### Day 5 - SQLAlchemy Basics
   - **Layers of abstraction in SQLAlchemy**
      - DBAPI like psycopg2 --> 
      - The dialect --> *helps to create a layer of abstraction for the flavor of sql used in the db , hence can use different sql flavors for different purposes like dev and production. Also makes it easier to switch db as per requirement*
      - Connection pool --> 
         - *abstracts the opening and closing of DB connections. Do not have to manually open and close connections to a DB.*
         - *Handles dropped connections to a db eg. network failure*
         - *reuse db connections*
         - *avoids making frequent calls to a db which is expensive*
      - Engine --> 
         - *lowest level of abstraction to interact with the DB very similar to interacting with DB using a DBAPI*
         - *One of the three layers which can be chosen to interact with the DB*
         - *Engine in SQLAlchemy refers to both connection pool and the dialect to interact with the DB*
         - *A connection pool is created everytime we create an SQLAlchemy engine*
      - SQL expressions -->
         - *Next layer of abstraction*
         - *use SQL expressions using python objects to interact with DB instead of sending raw SQL queries directly to the DB*
      - SQLAlchemy ORM -->
         - *Helps map python classes of objects to tables in the DB OR maps the DB schema to applications Python objects*
         - *Highest layer of abstraction*
         - *It abstracts the lower layers like the SQL expressions layers and the Engine layer to interact with the DB*
   
   - *Mapping between tables and classes* --> * Tables map to classes * Table records map to objects *Table columns map to attributes of the class

   - **Sample Flask app with SQLAlchemy; filename: *hello(dot)py* .**
      ```
         <!-- app outputs the "Hello, <person-name> !" on the localhost root route when this app is run-->

         <!-- import flask and flask_sqlalchemy -->

         from flask import Flask
         from flask_sqlalchemy import SQLAlchemy

         <!-- instantiate the flask app -->

         app = Flask(__name__)

         <!-- configure the flask app to use db at SQLALCHEMY_DATABASE_URI -->

         app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://postgres:3120358@localhost:5432/persons'
         app.config['SQLALCHEMY_TRACK_MODIFICATIONS']=False

         <!-- Link an instance of the db using SQLAlchemy with the flask app -->

         db = SQLAlchemy(app)

         <!-- Create a model using db.model -->

         class Person(db.Model):
            __tablename__ = 'personinfo' //custom table name of db table
            <!-- set the columns for the table OR attributes to the python class(recall table columns map to class attributes) -->
            id = db.Column(db.Integer, primary_key=True)
            name = db.Column(db.String(), nullable=False) //setting datatype, setting constraints

            <!-- Customise query outputs in python interactive mode -->
            def __repr__(self):
               return f'<Person details >>> Person ID : {self.id} , name: {self.name}>'

         <!-- Detects models and creates table in case no such table exists. If another table with the same name (irrespective of schema definition) exists, the table would be dropped from the model -->

         db.create_all()

         <!--using python decorator to route the app when '/' is hit -->

         @app.route('/')
         def index():
            person = Person.query.first() // the first query in the table
            return 'Hello,' + person.name + ' !'

         # Alternative approach
         <!-- uncomment following lines when using terminal command `python hello.py` -->
         # if __name__ == '__main__':
         #     app.run()
      ```
   - The configuration is done using the following line

      `app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://          postgres:abcd123@localhost:5432/persons'`
      - *postgresql* --> dialect or the flavor of sql. Can also specify specific DBAPI to use by appending eg. `+psycopg2` to postgresql for using psycopg2 dbapi
      - *postgres* --> the username to login to the host machine
      - *password* --> optional, here abcd123
      - *localhost* --> the url/host address.Can be localhost or an AWS server address etc.
      - *port* --> connection port used on host, here 5432
      - *name of db* --> name of the DB, here 'persons'

   - Flask-SQLAlchemy provides 1 to 1 mapping to SQLAlchemy data types . Some common data types are -->
      - integer
      - string(size) -- in postgresql not specifying the size results in a varchar type of data of variable length
      - DateTime -- expressed as a python datetime object
      - float
      - boolean
   
   - *Column constraints* Setting column constraints helps to ensure **data integrity** allowing for DB accuracy and DB consistency. It does not allow data to be inserted ina db if the constraints are not met. Constraints are set in `db.Column()`.eg. `db.checkConstraint('price>0')` ensures no entry with a negative price makes it to a db table.

   - **To create an entry in the db from terminal without starting a psql session**
      - For adding to the `persons` db above :
         - Create an instance of a Person object or multiple instances
            
            `person1 = Person(name="Ananta")`
            
            `person2 = Person(name="Mike")`
            *Note- no need to specify the id attribute on the above newly created objects since id being a primary key will be auto incremented by SQLAlchemy*
         - Add the entries as pending changes to the db

            `db.session.add(person1)` *if only one entry added*
            
            `db.session.add_all([person1, person2])` *if multiple entries added*
         - Commit these changes to the db for persistent data

            `db.session.commit()`

### Day6 - SQLAlchemy Objects Lifecycle

   - As we have seen earlier, interactions with a relational DB takes place as *transactions*. Transactions take place within a *session* which begin when a *connection* to the db is started and ends when the connection to the db is closed.

   - Within a session, *transactions* are created. Each transaction contains atomic bits of work we want to commit to the db. However, the changes are not immediately committed to the db and an object eg. an SQLAlchemy object passes through various stages before it is persistent to the db. This allows the ability to **undo** changes before committing to a db.

   - **SQLAlchemy Object Lifecycle**
      - **Transient** -- the object exists as soon as it is defined. *eg from previous code*   >>>>   `person1 = Person(name="Jimmy")`
      - **Pending** -- object attached to a session. At this state, `db.session.rollback()` becomes available to undo th changes. *eg* `db.session.add(person1)` At this stage, the object is waiting for a *flush* to happen. Updates and deletions to a db are all at this stage.
      - **Flushing** -- Flush takes pending changes and translates to SQL commands ready to be committed to the db. Referring the layers of abstraction of an ORM based db connection,
         - objects in the ORM are converted to SQL expressions.
         - SQL expressions are converted to SQL commands which the Engine can understand
         - These commands from engine are bundled into connections in the connections abstraction layer(*remember- all sql operations take place in form of transactions which reside within a session which are created when a connection to a db is created*)
         - These commands are also bundled into specific flavor of the SQL in the dialect layer.
         - These are handled at the lowest level by the DBAPI
      - **Committed** -  An object can either be rolled back or be flushed but both these are pending states. To make the data persistent once the object has been flushed , manually committing is still necessary. Committing persists the data and clears the session transactions for a new set of changes.
         
      - **Note** 
         - `db.session.rollback()` is the Transient stage of an object.
         - **Stages** ---- Transient >>>> Pending >>>> Flushed >>>> Committed
         
### Day 7 - Flushing in SQLAlchemy and Model.Query

   - Flush takes a pending change and translates it into SQL commands ready to be committed to the DB. On an INSERT sql command, it allows a *future* primary key value to exist. However, this future value has still not persisted to the DB and needs to be manually committed to make it persistent in the DB. 
   - Flush occurs when we call a **valid** `Query` on the SQLAlchemy object or when `db.session.commit()` is directly called. Flushing makes an in-memory object /table entry available if it is committed to the DB. 
   - Following image from the lesson with annotations added by me can help to clarify :
   ![Object lifecycle image](./images/object_lifecycle.png)

   - `db.Model.Query` provides the **Query** object that allows to `SELECT` from the DB to query and return data from the DB. Query allows *method chaining* and hence we can call multiple methods on the Query object getting more query object until it returns a *non query* object in the final function to fetch data. eg. filter(), filter_by(),count(),all().

### Day 8 : Building a CRUD app using Flask, SQLAlchemy and Jinja template library and MVC architecture

   - CRUD stands for Create, Read, Update and Delete. These are equivalent to following operations on a DB and SQLAlchemy ORM respectively
      - *Create* ----> INSERT -----> db.session.add('objectname')
      - *Read* -----> SELECT -----> Model.query.all()
      - *Update* -----> UPDATE -----> eg. vehicle.make = 'Nissan' where vehicle is an object of Class Vehicle and `make` is an attribute of the class.
      - *Delete* -----> DELETE -----> db.session.delete('objectname')
   
   - I am dividing the CRUD app into several parts each building on top of the other which would progressively help us to understand the method of building one.

   - **Sample code 1 - A simple app to render a todo list to the client using hard coded data on the server side flask app**

   *index.html*
   ```
   <!DOCTYPE html>
   <html lang="en">

   <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Todo App</title>
   </head>

   <body>
      <ul>
         <!-- Jinja templating: for loop over the list of 'data' received from the flask server app -->
         {% for d in data %}
         <li>{{d. description}}</li>
         {% endfor %}
      </ul>
   </body>

   </html>
   ```
   *app.py*
   ```
   <!-- require flask, sqlalchemy and render template -->
   from flask import Flask, render_template
   from flask_sqlalchemy import SQLAlchemy

   <!-- initialise flask app and setup configuration -->
   app = Flask(__name__)
   app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://postgres:<password>@localhost:5432/todos'
   app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

   <!-- Connect the SQLAlchemy ORM with the flask app -->
   db = SQLAlchemy(app)

   <!-- Define object model -->
   class Todo(db.Model):
      id = db.Column(db.Integer, primary_key=True)
      description = db.Column(db.String(), nullable = False)

      def __repr__(self):
         return f'{self.id} {self.description}'

   <!-- Create the  tables in the Db from the models if not already existing -->
   db.create_all()

   <!-- define default route for the app -->
   @app.route('/')
   def index():
      <!-- render a hard coded list of todo items -->
      return render_template('index.html',data=[
         {
            'description' : 'Todo item 1'
         },{
            'description' : 'Todo item 2'
         },{
            'description' : 'Todo item 3'
         }
      ])
   ```
   - To render a list of items from the DB we need to replace the hard coded list of values in the render template to fetch/read from the ORM model by replacing the `return` statement in the app.py code above with 
   `return render_template('index.html',data=Todo.query.all())` where `data` now contains all the objects within the Todo model from the db **todos**
   - **Model View Controller(MVC) pattern**
      - describes the 3 layers while developing an application
      - **Models** manage the data and business logic i.e what happens within the DB and the relationships across web app objects.
      - **View** -  relates to the representation and display logic i.e how the app is visible to the end user
      - **Controller** - handles the relationship between model and view. It routes the commands to the models and view thus deciding how the model and view interact with each other. A controller can directly update a view or may send a command to the model which changes the data inside a db and in turn causes the view to update.
      - Example app code above modularises into the MVC architecture as shown below: 
      ![MVC model](./images/MVC_todo.png) 
   - **Getting user input in a Flask application**
      - Three ways of receiving data from a user in a flask app:
         - URL query parameters --> `request.args.get(<key>)` where the query is a key:value pair
         *eg. `color = request.args.get('color')`*
         - Form inputs --> `request.form.get(<name>)` where the `name` is the attribute of an input field in the `form` element.
         *eg. `password = request.form.get('password')*
         - JSON --> Modern way of handling data from the user. The `request.data` retrieves the JSON data as a string. calling `json.loads` on the `request.data` string turns it to a list and dictionary in Python. 
         eg. 
         ```
         data_string = request.data
         data_dictionary = json.loads(data_string)
         ```

### Day 10 - CRUD App using Flask

   - Client side code to add a todo item

   ```
   <!DOCTYPE html>
   <html lang="en">

   <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Todo App</title>
   </head>

   <body>
      <form action="/todos/create" method="POST">
         <div>
               <label for="description">Add Todo item</label>
               <input type="text" name="todoItem" id="description">
         </div>
         <div>
               <input type="submit" id="submit" value="Create">
         </div>
      </form>
      <ul>
         <!-- Jinja templating: for loop -->
         {% for d in data %}
         <li>{{d. description}}</li>
         {% endfor %}
      </ul>
   </body>

   </html>
   ```
   - Server side code adding in the extra `create/todos/` route

   ```
   from flask import Flask, render_template, request,redirect, url_for
   from flask_sqlalchemy import SQLAlchemy

   app = Flask(__name__)
   app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://postgres:3120358@localhost:5432/todos'
   app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
   db = SQLAlchemy(app)

   class Todo(db.Model):
      id = db.Column(db.Integer, primary_key=True)
      description = db.Column(db.String(), nullable = False)

      def __repr__(self):
         return f'{self.id} {self.description}'

   db.create_all()

   @app.route('/')
   def index():
      todoList = Todo.query.all()
      print(todoList)
      return render_template('index.html', data=Todo.query.all())

   # Listen to 'create' route
   @app.route('/todos/create', methods=['POST'])
   def create():
      todoItem = request.form.get('todoItem')
      itemToAdd = Todo(description=todoItem)
      db.session.add(itemToAdd)
      db.session.commit()
      return redirect(url_for('index'))
   ```

   - **MVC flow in the above app** --> User enters info in form and clicks Create >>>> The view sends a POST request on the specified route >>>> The flask server then handles the incoming POST request and works on the DB model >>>> Upon making changes to the DB, it notifies the view of the changes and a new view is re rendered.

   - Forms can send data to a server by using `method` attribute by assigning either a `GET`value or `POST` value.Both GET and POST correspond to the HTTP methods.
      - **GET** ---> Sends a GET request to the server *through URL parameters* by appending the form data to url. Can handle such data on the flask server using `request.args['name']` or `request.args.get('name')`. Ideal for smaller submissions.
      - **POST** ---> Sends POST request to the server *through request body*. Can handle data sent by POST using `request.form.get('name')`. Request body *stringifies* the key-value pairs of form data received from the name attribute within the form inputs. 

   - `request` class is imported to handle the URL parameters. 
   - `url-for` method allows to generate a URL.
   - `redirect` returns a response object that redirects the client to the given URL.

   - **Sending asynchronous data to the server**
      - data from client can be sent asynchronously i.e data can be sent to the server and back to the client without a page refresh. Two methods used to implement this are `XMLHttpRequest` and `Fetch`. 
      - by default, the request sent to the server is synchronous. The request is sent to the server and the server upon completing its assigned task, forces the client to load the resource that the server dictates it to(eg. this was done by the `redirect(url_for(index))` in the above sample code).

         However, in case of an asynchronous request sent using either XHTTPRequest or the fetch method, the client itself decides how to change the view by manipulating the already loaded DOM depending on the response from the server.

      - **Using XHTTPRequest**
      
      ```
      <!-- xhttprequest object -->

      let xhttp = new XHTTPRequest();

      <!-- get the required data to send to server -->

      data = document.getElementById('todoItem').value;

      <!-- Open an xhttp connection to send data with specified method to the route -->

      xhttp.open('GET','/todos/create?description' + data);

      <!-- send the data to the server on specified route and close the connection -->

      xhttp.send();

      <!-- Event handler to call whenever **readyState** attribute changes -->
      xhttp.onreadystatechange = () => {
         if(this.readyState === 4 && this.status === 200) {
            console.log(xhttp.responseText)
         }
      });
      ```

      - **using fetch**
      ```
      fetch('todos/create',{
         method: 'POST',
         body: JSON.stringify({
            'description' : data
         }),
         headers: {
            'Content-Type' : 'application/json'
         }
      })
      ```
   - **sending AJAX requests using fetch ~ detailed**
      - Instead of the server dictating which view should be rendered once it finishes handling the data and updating the db , in the following code example, using the *promise based **fetch** api*, the view is updated whenever a new todo item is added without a page refresh.The broad events that take place in this async request are: 
         - User enters todo item 
         - clicks create 
         - the default behavior on form submit is disabled. Instead, fetch is called by passing in the desired url on server to which the request(here POST) is made. 
         - The *request type* is POST, with the body sent as a string using `json.stringify` (since data sent to web server is of type string) and alongwith it the *header* to indicate the request type data is of json type.
         - The server handles the incoming request. Extracts the desired data from the incoming request which is of type String using `request.get_json` to parse it as JSON.
         - DB connection is opened, changes added to db and changes committed.
         - Send the data back to the browser by turning the *JSON output into a Response object of mimetype application/json* using `jsonify()`
         - Client handles the received data in the `then`methods of the fetch method. Since `fetch` is **promise based**, hence the `then` executes ONLY when a response is received. In the `catch` method, errors are handled.

      *index.html*
      ```
      <!DOCTYPE html>
      <html lang="en">

      <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0">
         <title>Todo App</title>
         <style>
            .hidden {
                  color: red;
                  font-weight: 700;
                  display: none
            }
         </style>
      </head>

      <body>
         <form>
            <div>
                  <label for="description">Add Todo item</label>
                  <input type="text" name="todoItem" id="description">
            </div>
            <div>
                  <input type="submit" id="submit" value="Create">
            </div>
         </form>
         <div id="errorText" class="hidden">Something went wrong!</div>
         <ul>
            <!-- Jinja templating: for loop -->
            {% for d in data %}
            <li>{{d. description}}</li>
            {% endfor %}
         </ul>

         <!-- Prevent default form behavior -->
         <script>
            document.querySelector('form').onsubmit = (e) => {
                  e.preventDefault();
                  fetch('/todos/create', {
                     method: 'POST',
                     body: JSON.stringify({
                        'description': document.getElementById('description').value
                     }),
                     headers: {
                        'Content-Type': 'application/json'
                     }
                  })
                     .then(response => response.json())
                     .then(data => {
                        console.log('Data >>', data)
                        const liItem = document.createElement('li')
                        liItem.innerHTML = data['description']
                        document.querySelector('ul').appendChild(liItem)
                        document.getElementById('errorText').className = 'hidden'
                     })
                     .catch((err) => {
                        console.log("Error >>> ", err)
                        document.getElementById('errorText').className = ''
                     })
            }
         </script>
      </body>

      </html>
      ```

      *app.py*
      ```
      from flask import Flask, render_template, request,redirect, url_for, jsonify
      from flask_sqlalchemy import SQLAlchemy

      app = Flask(__name__)
      app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://postgres:3120358@localhost:5432/todos'
      app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
      db = SQLAlchemy(app)

      class Todo(db.Model):
         id = db.Column(db.Integer, primary_key=True)
         description = db.Column(db.String(), nullable = False)

         def __repr__(self):
            return f'{self.id} {self.description}'

      db.create_all()

      @app.route('/')
      def index():
         todoList = Todo.query.all()
         print(todoList)
         return render_template('index.html', data=Todo.query.all())

      # Listen to 'create' route
      @app.route('/todos/create', methods=['POST'])
      def create():
         todoItem = request.get_json()['description']
         itemToAdd = Todo(description=todoItem)
         db.session.add(itemToAdd)
         db.session.commit()
         return jsonify({
            'description': itemToAdd.description
         })
      ```

### Day 11 - CRUD App using Flask
   - **Using db session in controllers : Why use them?** To handle errors in case a db operation did not commit successfully or just partially, we can use the `db.session.rollback()` to undo any pending changes to the db. This can be done using the inbuilt exception handling capability of Python - `try except finally` block. 
   - *Sample server side code*

   ```
   @app.route('/todos/create', methods=['POST'])
   def create():
    todoItem = request.get_json()['description']
    body = {}
    error = False
    try:
        itemToAdd = Todo(description=todoItem)
        db.session.add(itemToAdd)
        db.session.commit()
        body['description'] = itemToAdd.description
    except:
        error = True
        db.session.rollback()
        print(sys.exc_info())
    finally:
        db.session.close()
    if not error:
        print('Sending data >>>' , body)
        return jsonify(body)
    else :
        abort(400)
   ```

   - The above code sample handles an error while committing to the db. Also uses a variable to store the data to be returned back to the client instead of trying to access the db object which is no longer available after committing to db since `db.session.commit` commits to the db and closes the connection. If we try to access an object from this instance after a commit is done, we cannot get that instance and a `DetachedInstanceError` is thrown.

   - **Migrations - changes in data schema**
      - Data migrations relate to how data schema is changed over time over an already existing db schema.
      - Db migration is challenging because any wrong changes to a db can break the app. One of the important features of a db is the ability to *rollback* the changes if any mistakes are made while making changes to the db. *Test* before making changes to the db.
      - **Why migrate dbs?** If we want to make changes to ourdb schema eg. add a new column *completed* to our todo app, we need to add in `completed = db.Column(...)`, followed by `db.create_all()` command. Then we need to `drop` the old db and `createdb` to create the entire db once again when the app is run. This is not only an expensive process to just add once column but also leads to loss of previously stored data. Hence, migrations in db is the way forward.
      - **Schema Migration** --> File that keeps track of changes to the db schema(i.e the structure of the db). It provides a *version history* of the db schema. Migrations allow to upgrade the db schema by *applying migrations* while they also allow rollback of db schema to a previous version.
      - **Migration key points:**
         - uniquely named.
         - contains changes made to the db schema over time.
         - normally stored in a local *migrations/ folder*
         - has *1 to 1 mapping* with the *changes* made to db and the *migration files* in the migrations/ folder.
         - migration files setup the tables in the db 
         - All changes made to the db schema must be present in the migrations folder.
      - To setup migrations(migration library for SQLAlchemy based migrations is *flask-migrate*) from the terminal/command line, a migration script is run(*flask-script* for flask apps). Migration scripts provide three basic features-
         - **migrate** --> create a migration script template ; generate a migration file based on changes to be made.
         - **upgrade** --> applying migrations that haven't yet been applied i.e *upgrading db*
         - **downgrade** --> rolling back migrations i.e *downgrading db*
      - *flask_migrate* is a migration manager for SQLAlchemy DBs based on the Alembic library. The *Alembic* is a lightweight db migration tool used for SQLAlchemy based DBs. flask-migrate sets up the configuration for Alembic to work properly with Flask and flask SQLAlchemy apps.
      - Steps to include migrations in project --->
         - initialise the migration repo for storing migrations.
         - create a migration script using `flask_migrate`
         - run the migration script manually using `flask_script`
   - Steps to implement migrations in flask app : 
      - Bootstrap migration : link to the flask app and db. 
      - Run `flask db init` to create a migration repository. *There is no need for `db.create_all()` anymore*
      - Migration allows us to keep track of the changes to db schema. We can make changes to our db models, allow flask migrate to incorporate those changes, *fine tune the migration scripts* and finally run `db.upgrade` to implement those changes. 


### Day 12-14 Complete CRUD application using Flask and SQLAlchemy

*app.py*

```
from flask import Flask, render_template, request,redirect, url_for, jsonify, abort
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate
import sys

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://postgres:3120358@localhost:5432/todos'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)

# Bootstrap db migrate commands: 
# Connect migrate to the app and db
migrate = Migrate(app,db)

class Todo(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    description = db.Column(db.String(), nullable = False)
    completed = db.Column(db.Boolean, nullable = False, default = False)

    def __repr__(self):
        return f'{self.id} {self.description}'

# db.create_all()

@app.route('/', methods=['GET','DELETE','POST'])
def index():
    todoList = Todo.query.all()
    print(todoList)
    return render_template('index.html', data=Todo.query.order_by('id').all())

# Listen to 'create' route
@app.route('/todos/create', methods=['POST'])
def create():
    todoItem = request.get_json()['description']
    body = {}
    error = False
    try:
        itemToAdd = Todo(description=todoItem)
        db.session.add(itemToAdd)
        db.session.commit()
        body['id'] = itemToAdd.id
        body['description'] = itemToAdd.description
    except:
        error = True
        db.session.rollback()
        print(sys.exc_info())
    finally:
        db.session.close()
    if not error:
        print('Sending data >>>' , body)
        return jsonify(body)
    else :
        abort(400)

# Listen to 'completed' route
@app.route('/todos/<todoId>/completed', methods=['POST'])
def setCompleted(todoId):
    try:
        completed = request.get_json()['completed']
        todo = Todo.query.get(todoId)
        todo.completed = completed
        db.session.commit()
    except:
        db.session.rollback()
    finally:
        db.session.close()
    return redirect(url_for('index'))

# Listen to 'delete' route
@app.route('/todos/<todoId>/delete',methods = ['DELETE'])
def deleteTodo(todoId):
    try:
        print(todoId)
        Todo.query.filter_by(id=todoId).delete()
        db.session.commit()
        print('Deleted item with id from todo', todoId)
    except Exception as e:
        db.session.rollback()
        print('Error while deleting!!!!', e)
    finally:
        db.session.close()
    return jsonify({'success' : True})
```

*index.html*

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Todo App</title>
    <style>
        .hidden {
            color: red;
            font-weight: 700;
            display: none
        }

        ul {
            list-style-type: none;
            margin-top: 20px;
            padding: 0;
        }

        li {
            display: grid;
            grid-template-columns: 25px 30% 30px;
            grid-template-rows: 1fr;
            padding: 5px;
        }

        button {
            color: rgb(146, 13, 13);
            border: none;
        }
    </style>
</head>

<body>
    <form>
        <div>
            <label for="description">Add Todo item</label>
            <input type="text" name="todoItem" id="description">
        </div>
        <div>
            <input type="submit" id="submit" value="Create">
        </div>
    </form>
    <div id="errorText" class="hidden">Something went wrong!</div>
    <ul>
        <!-- Jinja templating: for loop -->
        {% for d in data %}
        <li>
            <input class="checkStatus" data-id="{{d.id}}" type="checkbox" {% if d.completed %} checked
                {% endif %}>{{d. description}}
            <button class="delete" data-id="{{d.id}}" type="button">&cross;</button>
        </li>
        {% endfor %}
    </ul>

    <!-- Prevent default form behavior -->
    <script>
        // create new todo DOM elements
        document.querySelector('form').onsubmit = (e) => {
            e.preventDefault();
            fetch('/todos/create', {
                method: 'POST',
                body: JSON.stringify({
                    'description': document.getElementById('description').value
                }),
                headers: {
                    'Content-Type': 'application/json'
                }
            })
                .then(response => response.json())
                .then(data => {
                    console.log('Data >>', data)
                    const liItem = document.createElement('li')
                    const checkLi = document.createElement('input')
                    const delBtn = document.createElement('button')
                    // set button attributes
                    delBtn.className = 'delete'
                    delBtn.type = 'button'
                    delBtn.setAttribute('data-id', data.id)
                    delBtn.innerHTML = '&cross;'
                    // set list item attibutes
                    checkLi.className = 'checkStatus'
                    checkLi.setAttribute('data-id', data.id)
                    checkLi.type = 'checkbox'
                    const liText = document.createTextNode(data['description'])
                    // Below line does not work; have to create a text node to get the input to be inside the li item
                    // liItem.innerHTML = data['description']
                    liItem.appendChild(checkLi)
                    liItem.appendChild(liText)
                    liItem.appendChild(delBtn)
                    document.querySelector('ul').appendChild(liItem)
                    document.getElementById('errorText').className = 'hidden'
                })
                .catch((err) => {
                    console.log("Error >>> ", err)
                    document.getElementById('errorText').className = ''
                })
        }

        // handle checked items
        let checkBoxes = document.querySelectorAll('.checkStatus')
        for (let i = 0; i < checkBoxes.length; i++) {
            checkBoxes[i].onchange = (e) => {
                console.log('Event >>> ', e)
                const isChecked = e.target.checked
                const todoId = e.target.dataset['id']
                console.log(todoId)
                fetch('/todos/' + todoId + '/completed', {
                    method: 'POST',
                    body: JSON.stringify({
                        'completed': isChecked
                    }),
                    headers: {
                        'Content-Type': 'application/json'
                    }
                })
                    .then(() => document.getElementById('errorText').className = 'hidden')
                    .catch((err) => {
                        console.log("Error >>> ", err)
                        document.getElementById('errorText').className = ''
                    })
            }
        }

        // handle delete
        let buttonsList = document.querySelectorAll('.delete')
        for (let i = 0; i < buttonsList.length; i++) {
            buttonsList[i].addEventListener('click', (e) => {
                const buttonId = e.target.dataset['id']
                console.log('Event >>> ', e)
                fetch('/todos/' + buttonId + '/delete', {
                    method: 'DELETE',
                })
                    .then(() => {
                        document.getElementById('errorText').className = 'hidden'
                        console.log('Got response from server')
                        const itemToDelete = e.target.parentElement
                        itemToDelete.remove()
                        })
                    .catch((err) => {
                        console.log("Error >>> ", err)
                        document.getElementById('errorText').className = ''
                    })
            }
            )
        }

    </script>
</body>

</html>
```

### Day 15 - Modeling relationships in a multi model app
   - SQLAlchemy configures the relationship between multiple models in the app *once* and generates *JOIN* statements everytime it is required by the user.
   - `db.relationship` ---> SQLAlchemy provides and configures the relationship between models using this command. It is defined
   - `db.relationship` is defined on the **parent model**. it sets the name of the children and the name of the parent on a child using the `backref` attribute. eg. 
   
   ```
   <!-- SomeChild is name of the child class  &  some_parent 
   is the custom property name for the parent object -->
   class SomeParent(db.Model):
      id = db.Column(db.Integer, primary_key=True)
      country = db.Country(db.String(),nullable=False)
      children = db.relationship('SomeChild', backref='some_parent')
   ```
   
   - `db.relationship`([Relationship API](https://docs.sqlalchemy.org/en/13/orm/relationship_api.html)) also configures when the joined assets between child and parent should be loaded. This is **important** because **joins are expensive** and singnificantly impact the user experience(UX). 
   - *Lazy vs Eager loading of joined assets*
      - **Lazy** --> load needed joined data only as needed. Advantage is there is no initial wait time and the joined asset is loaded only when needed .Disadvantage is that it is loaded everytime there is a request for the joined asset. This is the default value for loading in SQLAlchemy. Set using `lazy=True` or `lazy=select`
      - **Eager** --> load all the joined assets in one go. Hence, reduces the future queries made to the DB. Subsequent SQL calls read the existing data. However, this comes at the cost of a long initial load time. Set using `lazy='joined'`

### Day 16
   - *Configure foreign keys* --> 
      -`db.relationship` **does not set foreign keys**. A column needs to be added (primary key from the parent table) on the child table which has the foreign key constraint. 
      - The **db.relationship** is set on the **parent model** while **foreign keys** are set on the **child** model.
      - The foreign key is bound by **referential integrity** i.e its data type must match that on the parent table and a foreign key column always matches
      - Implemented using the `db.ForeignKey` option in `db.Column` to specify a foreign key constraint that maps to the primary key on foreign table.
      - `db.ForeignKey(some_parent.id)` where `some_parent` is the name of parent table, `id` is parent table primary key. 

### Day 17 - Todo App - adding lists and items therein

   - app.py

   ```
      from flask import Flask, render_template, request,redirect, url_for, jsonify, abort
   from flask_sqlalchemy import SQLAlchemy
   from flask_migrate import Migrate
   import sys

   app = Flask(__name__)
   app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://postgres:3120358@localhost:5432/todos'
   app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

   db = SQLAlchemy(app)
   migrate = Migrate(app,db)

   class Todo(db.Model):
      __tablename__ = 'todo'
      id = db.Column(db.Integer, primary_key=True)
      description = db.Column(db.String(), nullable = False)
      completed = db.Column(db.Boolean, nullable = False, default=False)
      list_id = db.Column(db.Integer, db.ForeignKey("todoLists.id"), nullable=False)

      def __repr__(self):
         return f'{self.id} {self.description}'

   class TodoList(db.Model):
      __tablename__ = "todoLists"
      id = db.Column(db.Integer, primary_key=True)
      name = db.Column(db.String(), nullable=False)
      todo = db.relationship('Todo', backref='list', lazy=True)

   # db.create_all()
   # home route
   @app.route('/', methods=['GET','DELETE','POST'])
   def index():
      return redirect(url_for('get_list_todo',list_id=1))

   # list route
   @app.route('/lists/<list_id>')
   def get_list_todo(list_id):
      todoList = Todo.query.all()
      print(todoList)
      return render_template('index.html',lists = TodoList.query.all(), todos = Todo.query.filter_by(list_id=list_id).order_by(Todo.id).all(), active_list = TodoList.query.get(list_id))

   # Listen to 'create todos' route
   @app.route('/todos/create', methods=['POST'])
   def create():
      todoItem = request.get_json()['description']
      itemCategory = request.get_json()['category']
      body = {}
      error = False
      try:
         itemToAdd = Todo(description=todoItem, list_id=itemCategory)
         db.session.add(itemToAdd)
         db.session.commit()
         body['id'] = itemToAdd.id
         body['description'] = itemToAdd.description
         body['category'] = itemToAdd.list_id
      except:
         error = True
         db.session.rollback()
         print(sys.exc_info())
      finally:
         db.session.close()
      if not error:
         print('Sending data >>>' , body)
         return jsonify(body)
      else :
         abort(400)

   # Listen to 'create list' route
   @app.route('/lists/create', methods=['POST'])
   def createList():
      listName = request.get_json()['listName']
      error = False
      body = {}
      try:
         listToAdd = TodoList(name=listName)
         db.session.add(listToAdd)
         db.session.commit()
         body['id'] = listToAdd.id
         body['listName'] = listToAdd.name
      except :
         error = True
         db.session.rollback()
         print(sys.exc_info())
      finally:
         db.session.close()
      if not error:
         print('Sending new list to client!')
         return jsonify(body)
      else:
         abort(400)

   # Listen to 'completed' route
   @app.route('/todos/<todoId>/completed', methods=['POST'])
   def setCompleted(todoId):
      try:
         completed = request.get_json()['completed']
         todo = Todo.query.get(todoId)
         todo.completed = completed
         db.session.commit()
      except:
         db.session.rollback()
      finally:
         db.session.close()
      return redirect(url_for('index'))

   # Listen to 'delete' route
   @app.route('/todos/<todoId>/delete',methods = ['DELETE'])
   def deleteTodo(todoId):
      try:
         print(todoId)
         Todo.query.filter_by(id=todoId).delete()
         db.session.commit()
         print('Deleted item with id from todo', todoId)
      except Exception as e:
         db.session.rollback()
         print('Error while deleting!!!!', e)
      finally:
         db.session.close()
      return jsonify({'success' : True})
   ```

   - index.html

   ```
   <!DOCTYPE html>
   <html lang="en">

   <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Todo App</title>
      <link href="https://fonts.googleapis.com/css2?family=Roboto+Condensed&display=swap" rel="stylesheet">
      <style>
         .hidden {
               color: red;
               font-weight: 700;
               display: none
         }

         .displayList {
               display: grid;
               grid-template-columns: 30% 70%;
               grid-template-rows: auto;
         }

         form {
               margin: 20px 0;
         }

         .form-wrapper {
               display: flex;
               justify-content: space-evenly;
         }

         label {
               color: rgb(27, 97, 202);
               text-transform: uppercase;
               font-family: 'Roboto Condensed', sans-serif;
         }

         button {
               width: 40px;
               height: 30px;
               background-color: rgba(252, 250, 245, 0.856);
         }

         a {
               text-decoration: none;
               color: blue;
         }

         h4 {
               margin: unset;
         }

         #newList,
         #description {
               padding: 3px;
               border-radius: 5px;
               border-color: rgb(97, 150, 219);
               box-shadow: none;
         }

         #listSubmit,
         #submit {
               color: white;
               background-color: crimson;
               border: 0;
               padding: 6px;
               border-radius: 5px;
         }

         ul.todos {
               list-style-type: none;
               margin-top: 20px;
               padding: 5px;
               display: grid;
               grid-template-columns: 1fr;
               grid-template-rows: 30px;
         }

         ul.lists {
               list-style-type: none;
               line-height: 2em;
               padding-right: 10%;
         }

         li.todoLists {
               display: grid;
               grid-template-columns: 15% 70% 40px;
               padding: 10px 2px;
               align-items: baseline;
         }

         li.todoItems {
               display: grid;
               grid-template-columns: 25px 30% 30px;
               grid-template-rows: 1fr;
               padding: 5px;
         }

         button {
               color: rgb(146, 13, 13);
               border: none;
         }
      </style>
   </head>

   <body>
      <div class="form-wrapper">
         <form class="listForm">
               <label for="newList">Add new List</label>
               <input type="text" name="todoList" id="newList" required>
               <input type="submit" id="listSubmit" value="Create List">
         </form>
         <form class="newTodo">
               <div>
                  <label for="description">Add Todo item</label>
                  <input type="text" name="todoItem" id="description" required>
                  <input type="submit" id="submit" value="Create Todo">
               </div>
         </form>
      </div>

      <div id="errorText" class="hidden">Something went wrong!</div>
      <div class="displayList">
         <ul class="lists">
               <h4>Lists</h4>
               {% for item in lists %}
               <li class="todoLists">
                  <input type="checkbox" data-id="l{{item.id}}" class="listCheck">
                  <a href="/lists/{{item.id}}" id="listName">{{item.name}}</a>
                  <button class="deleteList" data-id="{{item.id}}" type="button">&cross;</button>
                  {% endfor %}
               </li>
         </ul>
         <ul class="todos">
               <!-- Jinja templating: for loop -->
               <h4>{{active_list.name}}</h4>
               {% for item in todos %}
               <li class="todoItems">
                  <input class="checkStatus" data-id="{{item.id}}" type="checkbox" {% if item.completed %} checked
                     {% endif %}>{{item. description}}
                  <button class="delete" data-id="{{item.id}}" type="button">&cross;</button>
               </li>
               {% endfor %}
         </ul>
      </div>


      <!-- Prevent default form behavior -->
      <script>
         // handler for dynamic list items (handle delete & handle checked items)
         function handleList(event) {
               if (event.target.className === 'delete') {
                  const buttonId = event.target.dataset['id']
                  fetch('/todos/' + buttonId + '/delete', {
                     method: 'DELETE',
                  })
                     .then(() => {
                           document.getElementById('errorText').className = 'hidden'
                           console.log('Got response from server')
                           const itemToDelete = event.target.parentElement
                           itemToDelete.remove()
                     })
                     .catch((err) => {
                           console.log("Error >>> ", err)
                           document.getElementById('errorText').className = ''
                     })
               } else if (event.target.className === 'checkStatus') {
                  const isChecked = event.target.checked
                  const todoId = event.target.dataset['id']
                  console.log(todoId)
                  fetch('/todos/' + todoId + '/completed', {
                     method: 'POST',
                     body: JSON.stringify({
                           'completed': isChecked
                     }),
                     headers: {
                           'Content-Type': 'application/json'
                     }
                  })
                     .then(() => document.getElementById('errorText').className = 'hidden')
                     .catch((err) => {
                           console.log("Error >>> ", err)
                           document.getElementById('errorText').className = ''
                     })
               } else if (event.target.className === 'deleteList') {
                  console.log('Delete List clicked!')
               }
         }

         // add event listener to the parent ul element
         const todos = document.querySelector('.todos')
         todos.addEventListener('click', handleList)

         // create new todo DOM elements
         document.querySelector('.newTodo').onsubmit = (e) => {
               e.preventDefault();
               const regex = /\/\d/
               let itemCat = parseInt(document.URL.match(regex)[0].split('/').join(''))
               fetch('/todos/create', {
                  method: 'POST',
                  body: JSON.stringify({
                     'description': document.getElementById('description').value,
                     'category': itemCat
                  }),
                  headers: {
                     'Content-Type': 'application/json'
                  }
               })
                  .then(response => response.json())
                  .then(data => {
                     console.log('Data >>', data)
                     const liItem = document.createElement('li')
                     const checkLi = document.createElement('input')
                     const delBtn = document.createElement('button')
                     // set list attibutes
                     liItem.className = 'todoItems'
                     // set button attributes
                     delBtn.className = 'delete'
                     delBtn.type = 'button'
                     delBtn.setAttribute('data-id', data.id)
                     delBtn.innerHTML = '&cross;'
                     // set list item attibutes
                     checkLi.className = 'checkStatus'
                     checkLi.setAttribute('data-id', data.id)
                     checkLi.type = 'checkbox'
                     const liText = document.createTextNode(data['description'])
                     // Below line does not work; have to create a text node to get the input to be inside the li item
                     // liItem.innerHTML = data['description']
                     liItem.appendChild(checkLi)
                     liItem.appendChild(liText)
                     liItem.appendChild(delBtn)
                     document.querySelector('.todos').appendChild(liItem)
                     document.getElementById('errorText').className = 'hidden'
                  })
                  .catch((err) => {
                     console.log("Error >>> ", err)
                     document.getElementById('errorText').className = ''
                  })
         }

         // create new list
         document.querySelector('.listForm').onsubmit = (e) => {
               e.preventDefault()
               fetch('/lists/create', {
                  method: 'POST',
                  body: JSON.stringify({
                     'listName': document.getElementById('newList').value
                  }),
                  headers: {
                     'Content-Type': 'application/json'
                  }
               }).then(response => response.json())
                  .then(data => {
                     console.log('Data', data)
                     const liItem = document.createElement('li')
                     const inputItem = document.createElement('input')
                     const link = document.createElement('a')
                     const delBtn = document.createElement('button')
                     // set attributes for liItem
                     li.className = 'todoLists'
                     // set attributes for input 
                     inputItem.type = 'checkbox'
                     inputItem['data-id'] = 'l' + data.id
                     inputItem.className = 'listCheck'
                     // set attributes for link
                     link.href = "/lists/" + data.id
                     link['id'] = 'listName'
                     link.innerHTML = data.listName
                     // set attibutes for delBtn
                     delBtn.className = 'deleteList'
                     delBtn.type = 'button'
                     delBtn.setAttribute('data-id', data.id)
                     delBtn.innerHTML = '&cross;'
                  })
         }
      </script>
   </body>

   </html>
   ```
