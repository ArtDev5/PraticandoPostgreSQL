# Retrieve the start times of members' bookings

    Question: How can you produce a list of the start times for bookings by members named 'David Farrell'?

    Answer: select starttime from cd.bookings where memid = (select memid from cd.members where firstname = 'David' and surname = 'Farrell');

# Work out the start times of bookings for tennis courts

    Question: How can you produce a list of the start times for bookings for tennis courts, for the date '2012-09-21'? Return a list of start time and facility name pairings, ordered by the time.

    Answer: select starttime, name from cd.bookings inner join cd.facilities on    cd.facilities.facid = cd.bookings.facid
    where starttime >= '2012-09-21' and  starttime < '2012-09-22' and name in ('Tennis Court 1', 'Tennis Court 2') order by starttime;

# Produce a list of all members who have recommended another member

    Question: How can you output a list of all members who have recommended another member? Ensure that there are no duplicates in the list, and that results are ordered by (surname, firstname).

    Answer: select distinct request.firstname, request.surname from cd.members request inner join cd.members response on request.memid = response.recommendedby 
    order by surname, firstname;

# Produce a list of all members, along with their recommender

    Question: How can you output a list of all members, including the individual who recommended them (if any)? Ensure that results are ordered by (surname, firstname).

    Answer: select members.firstname, members.surname, recmembers.firstname as recname, recmembers.surname as recsurname
    from cd.members members left join cd.members recmembers on recmembers.memid = members.recommendedby
    order by surname, firstname;

# Procue a list of all members who have used a tennis court

    Question: How can you produce a list of all members who have used a tennis court? Include in your output the name of the court, and the name of the member formatted as a single column. Ensure no duplicate data, and order by the member name followed by the facility name.

    Answer: select distinct concat(members.firstname,' ', members.surname) as name, facilities.name as facility from cd.members members inner join
    cd.bookings bookings on members.memid = bookings.memid inner join cd.facilities facilities on
    bookings.facid = facilities.facid where facilities.name in ('Tennis Court 1', 'Tennis Court 2') order by name, facility;

# Produce a list of costly bookings

    Question: How can you produce a list of bookings on the day of 2012-09-14 which will cost the member (or guest) more than $30? Remember that guests have different costs to members (the listed costs are per half-hour 'slot'), and the guest user is always ID 0. Include in your output the name of the facility, the name of the member formatted as a single column, and the cost. Order by descending cost, and do not use any subqueries.

    Answer: select concat(members.firstname, ' ', members.surname) as member, fac.name as facility, 
    case when members.memid = 0 then books.slots*fac.guestcost
    else books.slots*fac.membercost end as cost
    from cd.bookings books inner join cd.facilities fac on books.facid = fac.facid
    inner join cd.members members on books.memid = members.memid
    where starttime >= '2012-09-14 00:00:00' and starttime < '2012-09-15' and 
    ((members.memid = 0 and books.slots*fac.guestcost > 30) or 
    (members.memid != 0 and books.slots*fac.membercost > 30))
    order by cost desc;

# Produce a list of all members, along with their recommender, using no joins.

    Question: How can you output a list of all members, including the individual who recommended them (if any), without using any joins? Ensure that there are no duplicates in the list, and that each firstname + surname pairing is formatted as a column and ordered.

    Answer: select distinct concat(firstname, ' ', surname) as name, (select 
    concat(firstname, ' ', surname) as recommender from cd.members recmembers
    where members.recommendedby = recmembers.memid) from cd.members members order by name

# Produce a list of costly bookings, using a subquery

    Question: The Produce a list of costly bookings exercise contained some messy logic: we had to calculate the booking cost in both the WHERE clause and the CASE statement. Try to simplify this calculation using subqueries. For reference, the question was:

    How can you produce a list of bookings on the day of 2012-09-14 which will cost the member (or guest) more than $30? Remember that guests have different costs to members (the listed costs are per half-hour 'slot'), and the guest user is always ID 0. Include in your output the name of the facility, the name of the member formatted as a single column, and the cost. Order by descending cost.

    Answer: select member, facility, cost from (
	select concat(memb.firstname, ' ', memb.surname) as member,
	fac.name as facility, case when memb.memid = 0 then 
	fac.guestcost * books.slots else fac.membercost*books.slots
	end as cost from cd.members memb inner join cd.bookings books on
	memb.memid = books.memid inner join cd.facilities fac on
	books.facid = fac.facid where books.starttime > '2012-09-14 00:00:00' and
	books.starttime < '2012-09-15'
    ) as result where cost > 30 order by cost desc;