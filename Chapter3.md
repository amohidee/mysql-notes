#### CHAPTER 3: Querying and Updating the Database
Example scenario: Continue with the example from chapter 4, where the info for members and therapists needs to be stored in tables. However, now that there are empty tables, information needs to be inserted and read.

I want to be a NOCD therapist, but I'm not official unitl I'm in that table. To insert data into a table use `INSERT INTO table_name (col1_name, col2_name, ...) VALUES (col1_value, col2_value, ...)`. To insert myself as NOCD's first therapist do:

    INSERT INTO therapists (first_name, last_name, description, date_joined) VALUES ('Anees', 'Mohideen', 'Likes learning MySQL', '2022-06-02')

![therapists image](https://github.com/amohidee/mysql-notes/blob/main/therapists1.png)

However, I can't do much as a therapist without a patient. Now to add NOCD's first member with me as a therapist:
    
    INSERT INTO members (first_name, last_name, description, date_joined, therapist_id) VALUES ('Jane', 'Doe', 'N/A', '2022-06-02', 1)

![members image](https://github.com/amohidee/mysql-notes/blob/main/members1.png)

Notice that we can have therapist_id as whatever we want. At the moment there is no therapist with id = 2, so if we changed Jane Doe to have a therapist of 2 it wouldn't make any sense. Members sign up for NOCD to get therapy, but they can't if they aren't assigned to an actual therapist. To make sure that entries in one table match up with the existing `PRIMARY KEY` entries in another table use a `FOREIGN KEY`. We can use `ALTER TABLE` to add one:

    ALTER TABLE members
    ADD FOREIGN KEY (therapist_id) REFERENCES therapists(therapist_id);

If we try to add a member with therapist_id = 2 after this, we will get an error.

![foregin key error image](https://github.com/amohidee/mysql-notes/blob/main/foreignkeyerror.png)

Now that the database is up and running, NOCD can add some actual therapists and members. But now that data needs to be read and displayed. We want to be able to see what therapists and members NOCD has. The most basic way to do this is to use SELECT. To display all the members with all of the columns:

    SELECT * FROM members;

The * means to display all columns, and the `FROM` specifies what table to pull the data from. In this case we are pulling from members. But we dont need all the data. For example, if we are listing all our members on our website, we dont want to also list therapist_id, date_joined, etc. because that data is not understandable by random visitors. To specify columns replace * like this:

    SELECT last_name FROM members; //last name only
    SELECT first_name, last_name FROM members; //first and last name

It's hard to remember all my patients, but the member table lists everyone's patients instead of just mine. Luckily, I do remember that my therapist_id was 1. I can use `WHERE` to list only my patients:

    SELECT first_name, last_name, description FROM members WHERE therapist_id = 1

Unfortunately, I am feeling a bit lonely because I don't know anyone else at NOCD. I want to find other therapists who also work at NOCD who share my interests. One of my interests happens to be MySQL, so I want to look for other therapists who also like MySQL. 

Names and numbers are simple because usually they are just one word, so you can search for them like `therapist_id = 1`, as we did earlier. But strings like descriptions are more complicated because they are rarely the exact same. The keyword `LIKE` is similar to `=` but it can be used in conjunction with `%` to find patterns inside strings. `%` represents all strings of any length, including zero and one characters. We can use these to search for other therapists who enjoy MySQL.

    SELECT first_name, last_name, description FROM therapists WHERE description LIKE '%MySQL%'

`%` is placed on both ends to signify that 'MySQL' can be in the middle, beginning or end of the descriptions string. If we had '%MySQL' then it could only be at the end and if we had 'MySQL%' then it could only be at the beginning.

I want to check if I'm still a therapist for NOCD, so I want to be able to find my name only from this table. Therefore, the first name must be 'Anees' and the last name must be 'Mohideen'. To use both in a `WHERE` clause, use the AND keyword to make sure both criteria are evaluated like this:

    SELECT first_name, last_name, description FROM therapists WHERE last_name = 'Mohideen' AND first_name ='Mohideen'

`OR` is similar, but it will return all results who satifisy at least one of the criteria. To find all therapists who like MySQL or Python:

    SELECT first_name, last_name, description FROM therapists WHERE description LIKE '%MySQL%' OR description LIKE '%Python%'

`NOT` is applied in from of a criteria, and will return only if the criteria is not evaluated. To avoid getting any Mohideens in our query of therapists:

    SELECT first_name, last_name, description FROM therapists WHERE NOT last_name = 'Mohideen'

`XOR` means that either of the two criteria are true, but not both. To get all therapists interested in MySQL or Python, but not both:

    SELECT first_name, last_name, description FROM therapists WHERE description LIKE '%MySQL%' XOR description LIKE '%Python%'

As of right now, therapists are having a difficult time finding their patients. They need to look in the therapists table to find their therapist_id, then check through every member in the members table to find which member has their therapist_id. Let's create a table that list's each member's name with their therapist's name, so therapists just have to look at one table.

So far we have just been pulling data from one table, but here we need to pull data from both tables and display it in the same place. We can use `JOIN`. To get decipherable result have a matching case so that rows from one table are matched to rows from the other table. Otherwise you will get a table that is the permuation of every row from one table combined with every row from another table. To match each member with their therapist in a list:

    SELECT members.first_name , members.last_name, therapists.first_name , therapists.last_name
    FROM members JOIN therapists 
    ON members.therapist_id = therapists.therapist_id 

![join unlabeled](https://github.com/amohidee/mysql-notes/blob/main/joinUnlabeled.png)

NOTE: If both tables use the same key name (therapist_id in this case), you can say USING (key_name) instead of using the ON keyword.
Therapists are finding it hard to read this table both the members and therapists columns have the same variable names. The AS keyword can be used  

    SELECT members.first_name AS 'Member First Name', members.last_name AS 'Member Last Name',
    therapists.first_name AS 'Therapist First Name', therapists.last_name AS 'Therapist Last Name'
    FROM members JOIN therapists 
    ON members.therapist_id = therapists.therapist_id

![join labeled](https://github.com/amohidee/mysql-notes/blob/main/joinLabeled.png)

Remember that we altered the table to have a foreign key, so now every member will show up with a proper therapist when using this `JOIN` statement. There are no mismatches.