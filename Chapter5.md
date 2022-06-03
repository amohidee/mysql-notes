#### CHAPTER 5: Aggregating Data and Nested Queries From our Member and Therapist Tables

Example scenario: We know how to query from and update our tables, but we have only scratched the surface in terms of what we can learn from our data. The company executives want to know more things about the members and therapists at NOCD so they can make their executive decisions, and simple listing queries are not enough. We need to be able to aggregate our data and glean much more specific and complex data from our tables.

The excecutive have many tables to read through and don't want to waste time deciphering my tables. A way to make data more clear and readable is to use `CONCAT` statements to combine strings from different columns into a sentence under just one column. Instead of listing member names and therapist names in 4 different columns, we can say [therapist name] is [member name]'s therapist.

    SELECT CONCAT(therapists.first_name, ' ', therapists.last_name, ' is ', members.first_name, ' ', members.last_name, "'s therapist") AS 'member-therapist pairs'
    FROM members JOIN therapists 
    ON members.therapist_id = therapists.therapist_id 

Another way to view data, instead of just listing out every entry, is to aggregate date. The excecutives want to know which therapists who are listed in the NOCD database they actually have to pay for work. They can't be giving paychecks to therapists who don't even have any patients. Lets display every therapist who has a member for NOCD.

![join labelel1](https://github.com/amohidee/mysql-notes/blob/main/joinLabeled1.png)

As shown above, I have two members. If we just list all the data using a normal `SELECT` and `JOIN`, I will show up twice. I don't need to be recieving the same paycheck twice (even if it would be nice). Therefore we can use a `GROUP BY`, which aggregates all rows that share the same value:

    SELECT therapists.first_name, therapists.last_name
    FROM members JOIN therapists
    USING (therapist_id)
    GROUP BY therapists.first_name, therapists.last_name

Now I only show up once:

![therapists distinct](https://github.com/amohidee/mysql-notes/blob/main/therapistsDistinct.png)

My duplicate name is no longer shown. The other values in the columns in the two rows with my name are aggregated but not displayed. Another function we could use here is `DISTINCT`, which removes dupicated rows rather than aggregating them. 
Some functions we can use to display aggregated data are:
`COUNT()`: returns the number of occurences of the thing specified in the parenthesis without a GROUP BY. Use * to count the number of times each row shows up in the table.
`AVG()`: Returns the average of the values in the specified column.
`MAX()/MIN()`: Returns the Max/Min of the values in the specified column.
`SUM()`: Returns the Sum of the values in the specified column

THe executives need to know how much they need to pay each therapist. To find the number of members that each therapist has, we can use the COUNT() function:

    SELECT therapists.first_name, therapists.last_name, COUNT(*) AS 'Number of members'
    FROM members JOIN therapists
    USING (therapist_id)
    GROUP BY therapists.first_name, therapists.last_name

The `HAVING` keyword is much like `WHERE`, but is used to create criteria based on aggregate functions. 
Therapists with 1 member can be counted as part-time and will recieve a different paycheck. We need to make a list of therapists with only one member so the executives can pay everyone who is part time easily:

    SELECT therapists.first_name, therapists.last_name, COUNT(*) AS 'Number of members'
    FROM members JOIN therapists
    USING (therapist_id)
    GROUP BY therapist_id
    HAVING COUNT(*) = 1

Nested queries consist of one query inside another. They are very powerful because they can concisely produce the results to very complicated searches. 
The company makes sure to check up on new therapists to guide them throgh being a new NOCD employee. To list the members that have joined the most recently:

    SELECT first_name, last_name
    FROM members
    WHERE date_joined = (SELECT MAX(date_joined)
    FROM members)

Now the new therapists can get more familiar with how the company runs.

This one qeury performs two steps in one:
1) Finds the most recent date that members have joined
2) Lists the members that joined on that date

There are clauses that we can use on the tables returned by nested queries;
`ANY`: A relation is true between a selected value and any one of the entries in the nested query
`ALL`: A relation is true between a selected value and all of the entries in the nested query
`IN`: A selected value is the same as one of the entries in the nested query
`EXISTS`: There is at least one entry in the nested query

Therapists who are overworked can't provide as well as a service as those who aren't as stressed out. We need to identify the members who are under the therapist with the most members, so we can transfer them to less busy therapists.
We can use the `IN` clause to do a more complex query: listing all the members of the therapist with the most members.
    
    SELECT members.first_name, members.last_name FROM members JOIN therapists USING (therapist_id)
    WHERE therapist_id IN(SELECT therapist_id
    FROM members JOIN therapists
    USING (therapist_id)
    GROUP BY therapist_id
    HAVING COUNT(*) IN (SELECT MAX(y.num)
    FROM (SELECT COUNT(*) AS num
    FROM members JOIN therapists USING (therapist_id) GROUP BY therapist_id) AS y))

This query has 3 nested queries:
1) Lists the number of members each therapists has
2) Find the max number of members
3) Finds the id of the therapist with the max number of members
4) Lists the members of this therapist