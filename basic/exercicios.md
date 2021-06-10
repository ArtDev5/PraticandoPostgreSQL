# Retrieve everything from a table
    Question: How can you retrieve all the information from the cd.facilities table?

    Answer: select * from cd.facilities;

# Retrieve specific columns from a table
    Question: You want to print out a list of all of the facilities and their cost to members. How would you retrieve a list of only facility names and costs?

    Answer: select name, membercost from cd.facilities;

# Control which rows are retrieved

    Question: How can you produce a list of facilities that charge a fee to members?

    Answer: select * from cd.facilities where membercost > 0;

# Control which rows are retrieved - part 2

    Question: How can you produce a list of facilities that charge a fee to members, and that fee is less than 1/50th of the monthly maintenance cost? Return the facid, facility name, member cost, and monthly maintenance of the facilities in question.

    Answer: select facid, name, membercost, 
            monthlymaintenance from cd.facilities 
            where membercost > 0 and membercost < (monthlymaintenance* 1/50);

# Basic string searches

    Question: How can you produce a list of all facilities with the word 'Tennis' in their name?

    Answer: select * from cd.facilities where name like '%Tennis%';

# Matching against multiple possible values

    Question: How can you retrieve the details of facilities with ID 1 and 5? Try to do it without using the OR operator.

    Answer: select * from cd.facilities where facid in (1, 5);

# Classify results into buckets

    Question: How can you produce a list of facilities, with each labelled as 'cheap' or 'expensive' depending on if their monthly maintenance cost is more than $100? Return the name and monthly maintenance of the facilities in question.

    Answer: select name, case when (monthlymaintenance > 100) then 'expensive'
            else 'cheap' end as cost from cd.facilities;

# Working with dates

    Question: How can you produce a list of members who joined after the start of September 2012? Return the memid, surname, firstname, and joindate of the members in question.

    Answer: select memid, surname, firstname, joindate from cd.members where joindate > '2012-09-01 00:00:00'

# Removing duplicates, and ordering results

    Question: How can you produce an ordered list of the first 10 surnames in the members table? The list must not contain duplicates.

    Answer: select distinct surname from cd.members order by surname limit 10;

# Combining results from multiple queries

    Question: You, for some reason, want a combined list of all surnames and all facility names. Yes, this is a contrived example :-). Produce that list!

    Answer: select name from cd.facilities union select surname from cd.members;

# Simple aggregation

    Question: You'd like to get the signup date of your last member. How can you retrieve this information?

    Answer: select max(joindate) as latest from cd.members;

# More aggregation

    Question: You'd like to get the first and last name of the last member(s) who signed up - not just the date. How can you do that?

    Answer: select firstname, surname, joindate from cd.members where joindate = (select max(joindate) from cd.members);
    