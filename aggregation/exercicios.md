# Count the number of facilities

    Question: For our first foray into aggregates, we're going to stick to something simple. We want to know how many facilities exist - simply produce a total count.

    Answer: select count(*) from cd.facilities

# Count the number of expensive facilities

    Question: Produce a count of the number of facilities that have a cost to guests of 10 or more.

    Answer: select count(*) from cd.facilities where guestcost >= 10

# Count the number of recommendations each member makes.

    Question: Produce a count of the number of recommendations each member has made. Order by member ID.

    Answer: select recommendedby, count(*) from cd.members
            where recommendedby is not null
            group by recommendedby
            order by recommendedby

# List the total slots booked per facility

    Question: Produce a list of the total number of slots booked per facility. For now, just produce an output table consisting of facility id and slots, sorted by facility id.

    Answer: select facid, sum(slots) from cd.bookings
            group by facid order by facid

# List the total slots booked per facility in a given month

    Question: Produce a list of the total number of slots booked per facility in the month of September 2012. Produce an output table consisting of facility id and slots, sorted by the number of slots.

    Answer: select facid, sum(slots) from cd.bookings 
            where starttime >= '2012-09-01 00:00:00' and
            starttime <= '2012-09-30 23:59:59'
            group by facid
            order by sum(slots)

# List the total slots booked per facility per month

    Question: Produce a list of the total number of slots booked per facility per month in the year of 2012. Produce an output table consisting of facility id and slots, sorted by the id and month.

    Answer: select facid, extract (month from starttime) as month, sum(slots)
            from cd.bookings
            where extract(year from starttime) = 2012
            group by facid, month
            order by facid, month

# Find the count of members who have made at least one booking

    Question: Find the total number of members (including guests) who have made at least one booking.

    Answer: select count(*) from (select distinct memid from cd.bookings) as count

# List facilities with more than 1000 slots booked

    Question: Produce a list of facilities with more than 1000 slots booked. Produce an output table consisting of facility id and slots, sorted by facility id.

    Answer: select facid, sum(slots) from cd.bookings
            group by facid
            having sum(slots) > 1000
            order by facid

# Find the total revenue of each facility

    Question: Produce a list of facilities along with their total revenue. The output table should consist of facility name and revenue, sorted by revenue. Remember that there's a different cost for guests and members!

    Answer: select fac.name, sum( books.slots * case when 
            books.memid = 0 then fac.guestcost 
            else fac.membercost end) as revenue from cd.facilities fac
            inner join cd.bookings books on fac.facid = books.facid
            group by fac.name
            order by revenue

# Find facilities with a total revenue less than 1000

    Question: Produce a list of facilities with a total revenue less than 1000. Produce an output table consisting of facility name and revenue, sorted by revenue. Remember that there's a different cost for guests and members!

    Answer: select name, revenue from (
            select fac.name, sum(books.slots * case when books.memid = 0
            then fac.guestcost else fac.membercost end) as revenue
            from cd.bookings books inner join cd.facilities fac 
            on books.facid = fac.facid group by fac.name) as result
            where revenue < 1000
            order by revenue

# Output the facility id that has the highest number of slots booked

    Question: Output the facility id that has the highest number of slots booked. For bonus points, try a version without a LIMIT clause. This version will probably look messy!

    Answer: select facid, sum(slots) as "Total Slots"
            from cd.bookings
            group by facid
            order by sum(slots) desc limit 1

# List the total slots booked per facility per month, part 2

    Question: Produce a list of the total number of slots booked per facility per month in the year of 2012. In this version, include output rows containing totals for all months per facility, and a total for all months for all facilities. The output table should consist of facility id, month and slots, sorted by the id and month. When calculating the aggregated values for all months and all facids, return null values in the month and facid columns.
					 
    Answer: select facid, extract(month from starttime) as month, sum(slots) as slots
            from cd.bookings
            where extract(year from starttime) = 2012
            group by rollup(facid, month)
            order by facid, month

# List the total hours booked per named facility ~~questão incompleta~~

    Question: Produce a list of the total number of hours booked per facility, remembering that a slot lasts half an hour. The output table should consist of the facility id, name, and hours booked, sorted by facility id. Try formatting the hours to two decimal places.

    Answer: select fac.facid, fac.name, trim(to_char(sum(books.slots)/2.0, '9999D99'))

            as "Total Hours" from cd.facilities fac
            inner join cd.bookings books on fac.facid = books.facid
            group by fac.facid
            order by fac.facid


# List each member's first booking after September 1st 2012

    Question: Produce a list of each member name, id, and their first booking after September 1st 2012. Order by member ID.

    Answer: select memb.surname, memb.firstname, memb.memid, min(books.starttime) as starttime 
            from cd.members memb inner join cd.bookings books on memb.memid = books.memid
            where books.starttime > '2012-09-01 00:00:00'
            group by memb.memid
            order by memb.memid

# Produce a list of member names, with each row containing the total member count

    Question: Produce a list of member names, with each row containing the total member count. Order by join date, and include guest members.

    Answer: select (select count(*) from cd.members) as count, firstname, surname
	        from cd.members
            order by joindate

# Produce a numbered list of members

    Question: Produce a monotonically increasing numbered list of members (including guests), ordered by their date of joining. Remember that member IDs are not guaranteed to be sequential.

    Answer: select row_number() over(order by joindate), firstname, surname
            from cd.members
            order by joindate

# Output the facility id that has the highest number of slots booked, again

    Question: Output the facility id that has the highest number of slots booked. Ensure that in the event of a tie, all tieing results get output.

    Answer: select facid, total from (
	        select facid, total, rank() over (order by total desc) rank from (
            select facid, sum(slots) total
	        from cd.bookings
	        group by facid
            ) as sumslots
	        ) as ranked
            where rank = 1

# Rank members by (rounded) hours used

    Question: Produce a list of members (including guests), along with the number of hours they've booked in facilities, rounded to the nearest ten hours. Rank them by this rounded figure, producing output of first name, surname, rounded hours, rank. Sort by rank, surname, and first name.

    Answer: select firstname, surname, hours, rank() over (order by hours desc) from
	(select firstname, surname,
	((sum(bks.slots)+10)/20)*10 as hours

    from cd.bookings bks
	inner join cd.members mems
	on bks.memid = mems.memid
	group by mems.memid
	) as subq
    order by rank, surname, firstname;

# Find the top three revenue generating facilities

    Question: Produce a list of the top three revenue generating facilities (including ties). Output facility name and rank, sorted by rank and facility name.

    Answer: select name, rank from (
            select facs.name as name, rank() over (order by sum(case when memid = 0
            then slots * facs.guestcost else slots* facs.membercost end) desc) as rank

            from cd.bookings books inner join cd.facilities facs on
            books.facid = facs.facid
            group by facs.name) as subquery
            where rank <= 3
            order by rank;

# Classify facilities by value

    Question: Classify facilities into equally sized groups of high, average, and low based on their revenue. Order by classification and facility name.

    Answer: select name, case when class = 1 then 'high'
            when class = 2 then 'average'
            else 'low' end revenue from (
            	select facs.name as name, ntile(3) over (order by sum(case
            	when memid = 0 then slots*guestcost else
            	slots*membercost end) desc) as class

              	from cd.bookings books inner join cd.facilities facs on
            	books.facid = facs.facid
              	group by facs.name) as subq
            	order by class, name

# Calculate the payback time for each facility

    Question: Based on the 3 complete months of data so far, calculate the amount of time each facility will take to repay its cost of ownership. Remember to take into account ongoing monthly maintenance. Output facility name and payback time in months, order by facility name. Don't worry about differences in month lengths, we're only looking for a rough value here!

    Answer: select facs.name as name, facs.initialoutlay/((sum(case
												   
            when memid = 0 then slots * facs.guestcost
            else slots * membercost end)/3) - facs.monthlymaintenance) as months

            from cd.bookings bks inner join cd.facilities facs on 
            bks.facid = facs.facid
            group by facs.facid
            order by name;

# Calculate a rolling average of total revenue

    Question: For each day in August 2012, calculate a rolling average of total revenue over the previous 15 days. Output should contain date and revenue columns, sorted by the date. Remember to account for the possibility of a day having zero revenue. This one's a bit tough, so don't be afraid to check out the hint!

    Answer: select dategen.date, (select sum(case
            when memid = 0 then slots * facs.guestcost
            else slots * membercost end) as rev
            
            from cd.bookings bks inner join cd.facilities facs on 
            bks.facid = facs.facid where bks.starttime > dategen.date - interval '14 days'
            and bks.starttime < dategen.date + interval '1 day')/15 as revenue
            
            from ( select 	cast(generate_series(timestamp '2012-08-01',
            '2012-08-31','1 day') as date) as date )  as dategen
            order by dategen.date