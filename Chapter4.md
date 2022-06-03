#### CHAPTER 4: Creating a Database and Tables for NOCD Members and Therapists

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