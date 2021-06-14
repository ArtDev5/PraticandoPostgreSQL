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

    Answer: select fac.facid, fac.name, format(sum(books.slots)/2, '#.00') as "Total Hours" from cd.facilities fac
        inner join cd.bookings books on fac.facid = books.facid
        group by fac.facid
        order by fac.facid