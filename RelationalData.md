# Relational Data in Azure
Relational databases **model collections of entities**, from the real world **as tables**. An entity can be anything you want to record information; tipically important objects and events. A table contains rows, and **each row represents a single instance** of an entity.

![relational database](img/relational2.jpg)

Relational tables are a format for structured data, each row in a table **has the same columns**, though in same cases, **not all columns need to have a value**, which can be empty (or NULL).
Each column stores data of a **specific datatype**. The available datatypes that you can use when defining a table **depends on the dasabase system** you are using, though there are standard datatypes difened by the *American National Standards Institute (ANSI)* that are supported by most database systems.

### Normalization
Normalization is a term used by database professionals for a schema desing proccess that **minimizes data duplication** and **enforces data integrity**.
There are many complex rules that define the proccess of refactoring data into various levels (forms) of normalization.
1. **Separate each entity into its own table**
2. **Separate each discrete attribute into its own column**
3. **Uniquely indentify instance (row) using a primary key**
4. **Use foreign key columns to link related entities**

#### Non normalized data

![non normalized](img/non-normalized.jpg)

Customer and product details **are duplicated** for each individual item sold; customer name and adderss, and product name and price **are combined** in the same cells

### Normalized data

![normalized](img/normalized.jpg)

Each entity (customer, product, order, lineitem) is stored in its own table, and each discrete attribute in its own column.
Recording each instance as a row in a entitiy-specific table **removes duplication of data**, to change data of an entity you **only need to modify the value in a single row**.
Decomposition of attributes into individual columns ensures that each **value is constrained to an appropriate datatype**. Additionally, creation of individual columns provides a level of granularity in the data for querying, you can easily **filter instance of entities for a specific attribute**.
Instances of each entity are **uniquely indentified** by an ID or other key value, known as a **primary key**. When one entity **references another**, the primary key of the related entity is stored as a **foreign key**. Typically a *relational database management system (RDBMS)* can **enforce referential integrity** to ensure that a value entered into a foreign key has an existing corresponding primary key in the related table.
In some cases, a key (primary or foreign) can be defined as a *composite key* based on a **unique combination of multiple columns**.

## SQL
Is used to communicate with a relational database. It's the standard language for RDBMS. SQL statements are used to perform tasks such as update data in a database, or retrieve data from a database.
Although SQL statements are part of the SQL standard, many database management systems also have their own aditional proprietary extensions. These extensions provide functionality not covered by the SQL standard, and include areas such as security management and programmability. Microsoft SQL Server and Azure database services that are based on the SQL Server database engine, use Transact-SQL (T-SQL), that includes proprietary extensions for writing stored procedures and triggers (application code that can be stored in the database), and managing user accounts.

Some popular dialects of SQL:
- Transact-SQL (T-SQL): Used by Microsoft SQL Server and Azure SQL services.
- pgSQL: extensions implemented in PostgreSQL
- Procedural Language/SQL (PL/SQL): used by Oracle

### SQL Statements
Are groupd in three main logical groups:
- *Data Definition Language (DDL)*
- *Data Control Language (DCL)*
- *Data Manipulation Language (DML)*

#### DDL Statements
Used to **create, modify, and remove** tables and other objects(stored procedures, views, and so on) in a database.

| Statement | Description |
| --------- | ----------- |
| CREATE | Create a new object in the database, such as a table or a view. |
| ALTER | Modify the structure of an object. such as altering a table to add a new column. |
| DROP | Remove an object from the database. |
| RENAME | Rename an existing object. |

The following statement creates a new database table, the items between the parentheses specify the details of each column, including the name, the datatype, whether the column must always contain a value (*NOT NULL*), and whether the data in the column is used to uniquely identify a row (*PRIMARY KEY*).

```SQL
CREATE TABLE Product
(
    ID INT PRIMARY KEY,
    Name VARCHAR(20) NOT NULL,
    Price DECIMAL NULL
);
```
The datatypes available for columns will vary between database management systems.

#### DCL Statements
Used to **manage access** to objects in a database by granting, denying, or revoking permissions to specific users or groups.

| Statement | Description |
| --------- | ----------- |
| GRANT | Grant permission to perform specific actions |
| DENY | Deny permission to perform specific actions |
| REVOKE | Remove a previous granted permission |

The following statement permits a user to read, insert and modify data in a table.

```SQL
GRANT SELECT, INSERT, UPDATE 
ON Product
TO user1;
```

#### DML Statements
Used to **manipulate** the rows in tables, enabling to retrieve (query) data, insert new rows, modify existing rows or delete rows.

| Statement | Description |
| --------- | ----------- |
| SELECT | Read rows from a table |
| INSERT | Insert new rows into a table |
| UPDATE | Modify data in existing rows |
| DELETE | Delete existing rows |

The basic form of an **INSERT** statement will **insert one row at a tim**e. By default **SELECT, UPDATE, and DELETE** statements **are applied to every row in a table**. Apply a **WHERE** clause with these statements to **specify criteria**, **only rows that match these criteria** will be selected updated or deleted.

The following statement select all columns from a table where the criteria matches.
```SQL
SELECT *
FROM Customer
WHERE City = 'Seattle';
```
To retrieve only specific columns from the table, list them in the SELECT clause.
```SQL
SELECT FirstName, LastName, Address, City
FROM Customer
WHERE City = 'Seattle';
```
If a query return many rows, they don't appear in any specific order. If you want to sort the data, add an **ORDER BY** clause. The data will be **sorted** by the specified column
```SQL
SELECT FirstName, LastName, Address, City
FROM Customer
WHERE City = 'Seattle'
ORDER BY LastName;
```
SELECT statements can retrieve data from multiple tables using **JOIN** clause. Join indicates how the rows in one table are connected with rows in other tables, **matching a foreign key** from one table and its associated **primary key** in the other table.

The following query joins two tables, making use of table *aliases* to abbreviate the table names when specifying which columns to retrieve and which columns to match in the JOIN clause.
```SQL
SELECT o.OrderNo, o.OrderDate, c.Address, c.City
FROM Order AS o
JOIN Customer AS c
ON o.Customer = c.ID
```
The following statement modify an existing row.
```SQL
UPDATE Customer
SET Address = '123 High St.'
WHERE ID = 1;
```
The **DELETE** statement removes rows from a table. Specify the table to delete and a WHERE clause that indentiies the rows to be deleted.
```SQL
DELETE FROM Product
WHERE ID = 162;
```
The **INSERT** statement specify a table and  columns in an **INTO** clause, and a list of values to be stored in these columns. **Standard SQL only supports inserting one row at a time**, some dialects support multiple **VALUES** clauses to add several rows at a time.
```SQL
INSERT INTO Product(ID, Name, PRICE)
VALUES (99, 'Drill', 4.99);
```

### Database Objects
In addition to tables, relational databases can contain other structures that help to optimize data organization, encapsulate programmatic actions, and improve the speed of access.

#### Views
Is a virtual table based on the result of a SELECT query.
```SQL
CREATE VIEW Deliveries
AS
SELECT o.OrderNo, o.OrderDate,
       c.FirstName, c.LastName, c.Address, c.City
FROM Order AS o JOIN Customer AS c
ON o.Customer = c.ID;
```
Querying the view and filter the data is much the same way as a table.
```SQL
SELECT OrderNo, OrderDate, LastName, Address
FROM Deliveries
WHERE City = 'Seattle';
```

#### Stored Procedure
Defines SQL statements that can be run on command. Used to encapsulate programmatic logic in a database for actions that applications need to perform when working with data.
Stored procedures with parameters create a flexibe solution for common actions.
```SQL
CREATE PROCEDURE RenameProduct
    @ProductID INT,
    @NewName VARCHAR(20)
AS
UPDATE Product
SET Name = @NewName
WHERE ID = @ProductID;
```
To rename a product, you can execute the stored procedure
```SQL
EXEC RenameProduct 201, 'Spanner';
```

#### Indexes
Index **helps search data in a table**. When creating an index in a database, you specify a column from the table, and the index contains a copy of this data in a sorted order, with pointers to the corresponding rows in the table. Whe run a query that specifies this column in the WHERE clause, the database management system can use this index to fetch data **more quickly** than if it had to scan throught the entire table row by row.

The following statement statement create an index
```SQL
CREATE INDEX idx_ProductName
ON Product(Name);
```

The index creates a tree-based structure that the database system's query optimizer uses to quickly find rows in the Product table based on a specified Name.

![index table](img/index.jpg)

Tables containing **few rows**, using indexes is **not more efficient** than simply reading the entire table. However, when a table has **many rows**, indexes can **dramatically improve the performance of queries**.
You can create **many indexes** on a table. However, indexes aren't free. An index **consumes storage space**, and each time you insert, update or delete data in a table, indexes for that table must be maintained. This additional work can **slow down** insert, update and delete operations. You must strike a **balance** between having **indexes** that speed up queries versus the **cost** of performing other operations.
Tables containing **few rows**, using indexes is **not more efficient** than simply reading the entire table. However, when a table has **many rows**, indexes can **dramatically improve the performance of queries**.
You can create **many indexes** on a table. However, indexes aren't free. An index **consumes storage space**, and each time you insert, update or delete data in a table, indexes for that table must be maintained. This additional work can **slow down** insert, update and delete operations. You must strike a **balance** between having **indexes** that speed up queries versus the **cost** of performing other operations.