# A Brief Introduction to SQL [[Part I]]

SQL is a standard language for storing, manipulating, and retrieving data in relational database systems (RDMS). Some examples of such RDMS are:

- MySQL
- Oracle
- MS Access
- Postgres

After installing MySQL on your system, make sure that your enviroment variables are all set. To boot MySQL, open your standard command line terminal and enter.

```sql
$ mysql -u root -p
```

Here root, specifier the username with which you want to login into your database. You will need to enter your password after this. You can also use MYSQL workbench instead of terminal. This makes your workflow better.
<br/>


---

## Table of Contents

1. [Users](#users)
   - Creating a new user
   - Setting Permissions
2. [Database](#database)
   - Creating DB
   - See existing databases
   - Selecting and using a database
3. [Tables](#tables)
   - Creating a table
   - Delete table
   - Data types
   - Insert Data row/record
4. [Querying Data](#querying)
   - The SELECT statement
   - Where Clause
5. [Delete & Update](#delupdate)
   - Delete Operation
   - Update row data

---

<a name="users"></a>

## 1. Users

### Creating A New User:

After loging into your sql shell, you can create a new user by the following command

```sql
    CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
```

Managing your database as a root user is a very bad practice and can be dangerous when you're dealing with sensitive enterprise data, so it is always recomended to first create a new user (from root) and specify the access roles and permissions for this user.

### Grant permissions:

After creating your new users, you would like to assign some permissions. To keep this cheat sheet simple, we will grant all permissions to this user. Please note that, granting all permissions to a user is a bad practice, but we will let this slip for the purpose of this tutorial. We will use the following queries:

```sql
    GRANT ALL PRIVILEGES ON * . * TO 'username'@'localhost';
    FLUSH PRIVILEGES;
```

The Query for grant permissions is followed by flush privileges because it reloads the grant tables in mysql database. This enables the effects to take place instantly wihtout the need to restart server.

### Show permissions for specific user

```sql
    SHOW GRANTS FOR 'username'@'localhost';
```

<a name="database"></a>

## 2. Database

Now we'll get started with some standard database queries. But first we need to create a database. It should be clear by now that in RDMS, data is stored in form of tables in a database.

### Creating a new database

Lets create a new database by the name of test so we can do some experiments on it.

```sql
    CREATE DATABASE test;
```

We create a new database with the **_CREATE DATABASE_** clause. The name of the database should be unique on your server. To see the list of databases on your server you can use the **_SHOW DATABASES_** command, which will query us a list of available databases.

```sql
    SHOW DATABASES;

    +--------------------+
    | Database           |
    +--------------------+
    | test              |
    | information_schema |
    | mysql              |
    | performance_schema |
    | sys                |
    +--------------------+
```

### Using a database

Now to perform queries, You have to **_USE_** the database which you want to work on.

```sql
    USE test
```

<a name="tables"></a>

## 3. Tables

Now we are ready to create our first database table. We will call it users, and it will store the information such as id, name, address etc.

### Creating a new Table

The syntax for creating a new table follows: <br/>

CREATE TABLE table_name( <br/>
column_1_definition, <br/>
column_2_definition, <br/>
..., <br/>
table_constraints <br/>
) <br/>

To explain it a little further, **_CREATE TABLE_** clause is followed by the name of the table, which takes definitions of each column. The columns definitions are seperated by space. <br/>

The following is the syntax for Column name definitions:<br/>

column_name data_type(length) [NOT NULL] [DEFAULT value] [AUTO_INCREMENT];

- data_type as the name suggests, specifies the which type of data is stored in this column. Ex: VARCHAR9255

- **_NOT NULL_** constrain makes sure that the column value is not null.

- **_DEFAULT value_** can be used to assign a default value for the column.

- **_AUTO_INCREMENT_** is used when you want to assign a value to the column automatically by incrementing its value whenever a new row is added to the table.

Let us get back to our test database and create a users table.

```sql
    CREATE TABLE users(
        id INT AUTO_INCREMENT,
        first_name VARCHAR(100),
        last_name  VARCHAR(100),
        email VARCHAR(75),
        house_address VARCHAR(255),
        register_date DATETIME,
        PRIMAY KEY(id)
    );
```

Primary key is a field added to the end of the column definition which specifies the column that should be treated as pk. In this case we are treating our user id as a primary case and want to specify an **_AUTO_INCREMENT_** clause so that it is automatically assigned to every user.

> > Make sure that your primary key is a unique value

Now you can use the **_SHOW TABLES_** command to view tables in your db.

### Delete a table

You can use the same **_DROP_** command that we used earlier with the database to delete a table.

```sql
    DROP users
```

### Inserting data into Table

So now we have created our first table and inserted columns attributes in it. Now all we need is some data. To insert data into MySQL we use the **_INSERT_** statement . <br/>

The Insert statement allows you to insert one or more rows into the table. The syntax for Inserting data rows is as follows: <br/>

INSERT INTO table_name(c1, c2, c3, . . . ) <br/>
VALUES (v1, v2, v3 . . .); <br/>

We have to make sure that the column names we specify match with our data entry. For our users table, we will insert data as follows:

```sql
    INSERT INTO users(first_name, last_name, email, house_address, register_date)
    VALUES ('John', 'Doe', 'johndoe@gmail.com', '26 second street NY', now());
```

In our above example, we have not inserted the id filed because we have specified it to be **_AUTO_INCREMENT_** which means it will be auto generated and incremented in each row.
**_now()_** is a function which is used to get the current date and time.

### Inserting Multiple rows

Inserting Multiple rows is same as inserting a single record, the only difference is that the values are seperated by commas.

```sql
    INSERT INTO users(first_name, last_name, email, house_address, register_date)
    VALUES ('Nathan', 'Dave', 'nateDave@gmail.com', '296/9 Dummy Lane', now()),
    ('Jane', 'Doe', 'jane@gmail.com', 'Rhode Island', now()),
    ('Netalie', 'Reeves', 'nat767@gmail.com', 'new hemisphere', now()),
    ('Shane', 'Borne', 'shanebore@gmail.com', 'Boston', now()),
    ('Johanthan', 'Cooper', 'johncooper123.com', 'Rhode Island', now()),
```

<a name="querying"></a>

## 4. Querying Data

We have already used the **_SELECT_** statement for viewing our table entries.

```sql
    SELECT * FROM users
```

Here, \* represents the wild card known as all. The above statement specifies, select ALL from the users table. <br/>

We can instead use the names of column to display the relevant information. Suppose you want to see only the first and the last name from the users table:

```sql
    SELECT first_name, last_name FROM users;

    +------------+-----------+
    | first_name | last_name |
    +------------+-----------+
    | John       | Doe       |
    | Nathan     | Dave      |
    | Jane       | Doe       |
    | Netalie    | Reeves    |
    | Shane      | Borne     |
    | Johanthan  | Cooper    |
    +------------+-----------+
```

### Where Clause

The **_WHERE_** clause comes in handy, when you want to filter your queried data based on some specific attributes. <br/> It is used to specify a condition while fetching the data from a table.
You can mix select statement with where to make some very powerful query filter.
Suppose I want to filter the first_name, email and the house_address of the users who live on Rhode Island

```sql
    SELECT first_name, email, house_address FROM users WHERE house_address="Rhode Island";
    +------------+-------------------+---------------+
    | first_name | email             | house_address |
    +------------+-------------------+---------------+
    | Jane       | jane@gmail.com    | Rhode Island  |
    | Johanthan  | johncooper123.com | Rhode Island  |
    +------------+-------------------+---------------+
```

Not only this, we can add multiple conditions. Say we want to query users who are from Rhode Island and named Jane.

```sql
        SELECT first_name, email, house_address FROM users WHERE house_address="Rhode Island" AND first_name="Jane";
    +------------+-------------------+---------------+
    | first_name | email             | house_address |
    +------------+-------------------+---------------+
    | Jane       | jane@gmail.com    | Rhode Island  |
    +------------+-------------------+---------------+
```

You can use all kinds of operators here, such as **_<_** or **_>_** to filter results. We will cover these operators later.

<a name="delupdate"></a>

## Delete & Update row data

### Deletion

Deletion can be done with the help of the **_DELETE_** clause. Mostly, it is used with the **_WHERE_** statement in reference to the primary key. In the below example we will delete a user with the primary key 5.

```sql
    DELETE FROM users WHERE id=5;
```

Now when you run **_SELECT _ FROM users;\***, you will observe that the user with primary key 5 is no longer in the table.

> > Please note that deletion is a very delicate operation. Be sure to use the WHERE clause, otherwise you may endup deleting the whole table.

### Update info

Update is one of the most frequent operations which is performed on a database. It can be undertaken with the help of the **_UPDATE_** and **_SET_** clause. The basic syntax for updating an entry is as follows: <br/>

UPDATE table_name
SET column1 = value1, column2 = value2...., columnN = valueN
WHERE [condition];

Lets say that Nathan had a typo in his email address and wants to change it.

```sql
    UPDATE users SET email="nathandave@gmail.com" WHERE id=2;
```
