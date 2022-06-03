CHAPTER 1:
Installing MySQL

CHAPTER 2:
Learned about how to properly model and design a database: Requirements Analysis -> Conceptual Design -> Logical Design. First write down what data you need from the database, then model it using the Entity Relationship model. Finally, translate this into the actual database. The Entity Relationship model is a way of describing how different tables are related to each other. Entities vs Attributes: Objects that are of direct interest to the database, have components of their own, or have multiple instances can all be entities. Otherwise they are attributes. Entity vs Relationship: Entities are nouns, relationships are verbs. Some times include an intermediate entity in a relationship (M:N = 2 * 2:N relationhips) Some types of entities are strong and weak, while there are M:N and 1:N relationships. Databases should be normalized. Try to remove repeating groups/redundant data and create diffferent tables for each attribute, relating them with the same key. Display the Entity-Relationship model of a database in MySQL Workshop.


#### CHAPTER 4:

Example scenario: We want to store information about NOCD members.
We need to have an environment where we can store and access relevant data for the members from different computers. To do this create a database

    CREATE DATABASE nocd;

To create tables in this database enter `USE nocd`

The data itself will be stored in the database, but needs to be stored in a way that we can access and understand, such as in rows and columns. We can use a table to do this.
Before making the table, we need to think about what data needs to be stored: id (so each patient has a completely unique identifier), first/last name, picture of member, short description of member, date joined, and therapist info

    CREATE TABLE members (
        user_id int NOT NULL AUTO_INCREMENT,
        first_name varchar(128) NOT NULL,
        last_name varchar(128) NOT NULL,
        image_url varchar(255),
        description TEXT,
        date_joined DATE,
        therapist_id INT,
        PRIMARY KEY (user_id)
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

Each line represents a different column. Each row that will be inserted in the future will represent a unique member. 
These members will have ids, names, picture, etc. as shown by the table.

Types of each column: `INT` is good to use for numbers, but you can use smaller variants like `SMALLINT` to save storage, or bigger ones like `BIGINT` if you want to go above 2,147,483,647.
Use varchar and a specified limit on the number of characters to save storage if you know the length of the string, but use TEXT if you dont konw the limit on the length of entries.
Use `DATE` to have entries in a standard YYYY-MM-DD format for dates.

`NOT NULL` means members cannot have this column be blank. It is necessary they have an id, and a name. However they dont need the other stuff like pictures or descriptions.
`AUTO_INCREMENT` means that we dont need to manually insert these values. Everytime we insert a new member into the table, they will automaticlly get an id one higher than 
the previous member.

`PRIMARY KEY` indicated that a column is completely unique for each row. You cannot insert a new row that has the same `PRIMARY KEY` as an existing row.
Members can have the same name but not the same id

However we need to add a column for the email of the members/therapists. We could create a new table with said column but we would need to delete existing columns.
Instead we can use `ALTER TABLE` to add a column to these tables

    ALTER TABLE members ADD member_email varchar(128);
    ALTER TABLE therapists ADD therapist_email varchar(128);

#### CHAPTER 3:
Example scenario: Continue with the example from chapter 4, where the info for members and therapists needs to be stored in tables. However, now that there are empty tables, information needs to be inserted and read.

To insert data into a table use `INSERT INTO table_name (col1_name, col2_name, ...) VALUES (col1_value, col2_value, ...)`. To insert myself as NOCD's first therapist do:

    INSERT INTO therapists (first_name, last_name, description, date_joined) VALUES ('Anees', 'Mohideen', 'Likes learning MySQL', '2022-06-02')

![therapists image](https://github.com/amohidee/mysql-notes/blob/main/therapists1.png)

Now to add NOCD's first member with me as a therapist:
    
    INSERT INTO members (first_name, last_name, description, date_joined, therapist_id) VALUES ('Jane', 'Doe', 'N/A', '2022-06-02', 1)

![members image](https://github.com/amohidee/mysql-notes/blob/main/members1.png)

Notice that we have therapist_id as whatever we want. At the moment there is no therapist with id = 2, so if we changed Jane Doe to have a therapist of 2 it wouldn't make any sense. To make sure that entries in one table match up with the existing `PRIMARY KEY` entries in another table use a `FOREIGN KEY`. We can use `ALTER TABLE` to add one:

    ALTER TABLE members
    ADD FOREIGN KEY (therapist_id) REFERENCES therapists(therapist_id);

If we try to add a member with therapist_id = 2 after this, we will get an error.

![foregin key error image](https://github.com/amohidee/mysql-notes/blob/main/foreignkeyerror.png)

Now that the database is up and running, NOCD can add some actual therapists and members. But now that data needs to be read and displayed. The most basic way to do this is to use SELECT. To display all the members with all of the columns:

    SELECT * FROM members;

The * means to display all columns, and the `FROM` speicifies what table to pull the data from. In this case we are pulling from members. But we dont need all the data. If we just want the last names, displayed date joined makes things confusing. To specify columns replace * like this:

    SELECT last_name FROM members;
    SELECT first_name, last_name FROM members;

Lets say we just want to look at all Mohideen's that are registered as therapists. We don't want to look at every single therapist. Use `WHERE`:

    SELECT first_name, last_name, description FROM therapists WHERE last_name = 'Mohideen'

Last names and first names are simple because usually they are just one word, so you can search for them like `last_name = 'Mohideen'`, as we did earlier. But strings like descriptions are more complicated because they are rarely the exact same. The keyword `LIKE` is similar to `=` but it can be used in conjunction with `%` to find patterns inside strings. `%` represents all strings of any length, including zero and one characters. We can use these to search for other therapists who enjoy MySQL.

    SELECT first_name, last_name, description FROM therapists WHERE description LIKE '%MySQL%'

`%` is placed on both ends to signify that 'MySQL' can be in the middle, beginning or end of the descriptions string. If we had '%MySQL' then it could only be at the end and if we had 'MySQL%' then it could only be at the beginning.

Earlier we queried for all Mohideens that were registered as therapists in the table. However, lets try and find me. Therefore, the first name must be 'Anees' and the last name must be 'Mohideen'. To use both in a `WHERE` clause, use the AND keyword to make sure both criteria are evaluated like this:

    SELECT first_name, last_name, description FROM therapists WHERE last_name = 'Mohideen' AND first_name ='Mohideen'

`OR` is similar, but it will return all results who satifisy at least one of the criteria. To find all therapists who like MySQL or Python:

    SELECT first_name, last_name, description FROM therapists WHERE description LIKE '%MySQL%' OR description LIKE '%Python%'

`NOT` is applied in from of a criteria, and will return only if the criteria is not evaluated. To avoid getting any Mohideens in our query of therapists:

    SELECT first_name, last_name, description FROM therapists WHERE NOT last_name = 'Mohideen'

`XOR` means that either of the two criteria are true, but not both. To get all therapists interested in MySQL or Python, but not both:

    SELECT first_name, last_name, description FROM therapists WHERE description LIKE '%MySQL%' XOR description LIKE '%Python%'

So far we have just been pulling data from one table, but what if we want to pull data from two tables and display it all at once? We can use `JOIN`. To get decipherable result have a matching case so that rows from one table are matched to rows from the other table. Otherwise you will get a table that is the permuation of every row from one table combined with every row from another table. To match each member with their therapist in a list:

    SELECT members.first_name AS 'Member First Name', members.last_name AS 'Member Last Name',
    therapists.first_name AS 'Therapist First Name', therapists.last_name AS 'Therapist Last Name'
    FROM members JOIN therapists 
    ON members.therapist_id = therapists.therapist_id    

Remember that we altered the table to have a foreign key, so now every member will show up with a proper therapist when using this `JOIN` statement. There are no mismatches.

//Unfinished
CHAPTER 5:
Give columns aliases using AS so that you can be more clear when displaying the table. Use the LIMIT clause to only list a specified number of rows.
Use CONCAT to concatonate string results together when using a SELECT to display the table. It can make displaying information a lot more clear, by putting
the data in sentences rather in strict columns.
Use AS to simplify complicated JOINs. Use DISTINCT to remove duplicates when doing a SELECT on a table.
GROUP BY aggregates all the rows that have the same column into one row. Use the HAVING clause to filter out results based on aggregated DATA (like COUNT).
INNER JOIN joins two tables, use USING to make sure that the rows that show up have a row where the cols from both tables match. Use UNION to combine the results of
mulitple SELECT statements into one list of rows. LEFT/RIGHT JOINS include rows that dont match the ON column from the respective table.
Use ANY as a reference to a set returned by a nested query. ANY specifies that the criteria has to match for only one of the results in the list.
ALL is similar, the criteria must match for every single result in the set returned by the nested query
EXISTS is used to check if a subquery returns any results. If the subquery has at least one result EXISTS is true, and if none then false.
NOT EXISTS is same but the opposite because of the NOT.


 