# Format the names of members

    Question: Output the names of all members, formatted as 'Surname, Firstname'

    Answer: select concat(surname, ', ', firstname) from cd.members

# Find facilities by a name prefix

    Question: Find all facilities whose name begins with 'Tennis'. Retrieve all columns.

    Answer: select * from cd.facilities where name like 'Tennis%'

# Perform a case-insensitive search

    Question: Perform a case-insensitive search to find all facilities whose name begins with 'tennis'. Retrieve all columns.

    Answer: select * from cd.facilities where upper(name) like 'TENNIS%'

# Find telephone numbers with parentheses

    Question: You've noticed that the club's member table has telephone numbers with very inconsistent formatting. You'd like to find all the telephone numbers that contain parentheses, returning the member ID and telephone number sorted by member ID.

    Answer: select memid, telephone from cd.members where telephone
            similar to '%[()]%'

# Pad zip codes with leading zeroes

    Question: The zip codes in our example dataset have had leading zeroes removed from them by virtue of being stored as a numeric type. Retrieve all zip codes from the members table, padding any zip codes less than 5 characters long with leading zeroes. Order by the new zip code.

    Answer: select lpad(cast(zipcode as char(5)),5,'0') zip from cd.members order by zip

# Count the number of members whose surname starts with each letter of the alphabet

    Question: You'd like to produce a count of how many members you have whose surname starts with each letter of the alphabet. Sort by the letter, and don't worry about printing out a letter if the count is 0.

    Answer: select substring(surname, 1, 1) as letter, count(*) from
            cd.members group by letter order by letter

# Clean up telephone numbers

    Question: The telephone numbers in the database are very inconsistently formatted. You'd like to print a list of member ids and numbers that have had '-','(',')', and ' ' characters removed. Order by member id.

    Answer: select memid, translate(telephone, '-() ', '') as telephone from cd.members
            order by memid