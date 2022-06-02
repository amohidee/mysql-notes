CHAPTER 1:
Installing MySQL

CHAPTER 2:
'Learned about how to properly model and design a database: Requirements Analysis -> Conceptual Design -> Logical Design. 
First write down what data you need from the database, then model it using the Entity Relationship model. Finally, translate this into the actual database. 
The Entity Relationship model is a way of describing how different tables are related to each other. 
Entities vs Attributes: Objects that are of direct interest to the database, have components of their own, or have multiple instances can all be entities. 
Otherwise they are attributes. Entity vs Relationship: Entities are nouns, relationships are verbs. 
Some times include an intermediate entity in a relationship (M:N = 2 * 2:N relationhips) 
Some types of entities are strong and weak, while there are M:N and 1:N relationships. Databases should be normalized. 
Try to remove repeating groups/redundant data and create diffferent tables for each attribute, relating them with the same key. 
Display the Entity-Relationship model of a database in MySQL Workshop.
'

CHAPTER 3:
'Learned how to use the terminal to look at the database (SHOW tables, USE sakila,). 
Used SELECT statements to look at tables and columns(SELECT * from city, SELECT city from city). 
Use WHERE to select specific rows with atributes (SELECT city FROM city WHERE country_id = 5). 
Combine the wildcard "%" and the LIKE function to look for strings that are only partially match the one in the WHERE. 
Combine conditions with AND, OR, NOT, XOR. Limit will restrict the number of results to the INT listed. ORDER BY sorts the list. 
JOIN will combine two tables. INSERT, DROP TABLE,TRUNCATE TABLE, DELETE FROM, UPDATE to change a table.
'
SELECT film from film_list WHERE price < 5 AND AND (category LIKE 'Action' OR category LIKE 'Animation') AND actor LIKE "%GABLE%"

SELECT city.name, country.name FROM city INNER JOIN country ON city.CountryCode = country.Code WHERE city.CountryCode = 'USA' ORDER BY city.name;

UPDATE actor SET last_name= UPPER('BALE'), last_update = NOW() WHERE first_name LIKE 'CHRISTIAN';


CHAPTER 4:
'
Example scenario: We want to store information about NOCD members.
We need to have an environment where we can store and access relevant data for the members from different computers. To do this create a database'

CREATE DATABASE nocd;

'The data itself will be stored in the database, but needs to be stored in a way that we can access and understand, such as in rows and columns. We can use a table to do this.
Before making the table, we need to think about what data needs to be stored: id (so each patient has a completely unique identifier), first/last name, picture of member,
short description of member, date joined, and therapist info (stored in similar table for therapists perhaps)'

CREATE TABLE members (
user_id int NOT NULL AUTO_INCREMENT,
first_name varchar(128) NOT NULL,
last_name varchar(128) NOT NULL,
image_url varchar(255),
description TEXT,
date_joined DATE,
therapist_id INT,
PRIMARY KEY (user_id)
FOREIGN KEY (therapist_id) REFERENCES therapists (therapist_id)
);

CREATE TABLE therapists (
therapist_id INT NOT NULL AUTO_INCREMENT,
first_name varchar(128) NOT NULL,
last_name varchar(128) NOT NULL,
image_url varchar(255),
description TEXT,
date_joined DATE,
PRIMARY KEY (therapist_id)
);

'Each line represents a different column. Each row that will be inserted in the future will represent a unique member. 
These members will have ids, names, picture, etc. as shown by the table.'

'Types of each column: INT is good to use for numbers, but you can use smaller variants like SMALLINT to save storage, or bigger ones like BIGINT if you want to go above 2,147,483,647.
Use varchar and a specified limit on the number of characters to save storage if you know the length of the string, but use TEXT if you dont konw the limit on the length of entries.
Use DATE to have entries in a standard YYYY-MM-DD format for dates.'

'NOT NULL means members cannot have this column be blank. It is necessary they have an id, and a name. However htey dont need the other stuff like pictures or descriptions.'
'AUTO_INCREMENT means that we dont need to manually insert these values. Everytime we insert a new member into the table, they will automaticlly get an id one higher than 
the previous member.'

'PRIMARY KEY indicated that a column is completely unique for each row. You cannot insert a new row that has the same PRIMARY KEY as an existing row.
Members can have the same name but not the same id'

'Foreign key ensures that the therapist ids entered are actual therapists from the therapists table. Otherwise we could enter whatever therapist_id we wanted without
it being an actual id. If you enter a therapist_id with FOREIGN KEY then you get an error. This is so that if we join the tables there are no mismatches.'

'However we need to add a column for the email of the members/therapists. We could create a new table with said column but we would need to delete existing columns.
Instead we can use ALTER TABLE to add a column to these tables'

ALTER TABLE members ADD member_email varchar(128);
ALTER TABLE therapists ADD therapist_email varchar(128);

CHAPTER 5:
'
Give columns aliases using AS so that you can be more clear when displaying the table. Use the LIMIT clause to only list a specified number of rows.
Use CONCAT to concatonate string results together when using a SELECT to display the table. It can make displaying information a lot more clear, by putting
the data in sentences rather in strict columns. Use AS to simplify complicated JOINs. Use DISTINCT to remove duplicates when doing a SELECT on a table.
GROUP BY aggregates all the rows that have the same column into one row. Use the HAVING clause to filter out results based on aggregated DATA (like COUNT).
INNER JOIN joins two tables, use USING to make sure that the rows that show up have a row where the cols from both tables match. Use UNION to combine the results of
mulitple SELECT statements into one list of rows. LEFT/RIGHT JOINS include rows that dont match the ON column from the respective table.
'
 