# Produce a timestamp for 1 a.m. on the 31st of August 2012

    Question: Produce a timestamp for 1 a.m. on the 31st of August 2012.

    Answer: select timestamp '2012-08-31 01:00:00';   

# Subtract timestamps from each other

    Question: Find the result of subtracting the timestamp '2012-07-30 01:00:00' from the timestamp '2012-08-31 01:00:00'

    Answer: select timestamp '2012-08-31 01:00:00' - timestamp '2012-07-30 01:00:00'

# Generate a list of all the dates in October 2012

    Question: Produce a list of all the dates in October 2012. They can be output as a timestamp (with time set to midnight) or a date.

    Answer: select generate_series(timestamp '2012-10-01 00:00:00', timestamp '2012-10-31 00:00:00', '1 day')

# Get the day of the month from a timestamp

    Question: Get the day of the month from the timestamp '2012-08-31' as an integer.

    Answer: select extract(day from timestamp '2012-08-31') as day

# Work out the number of seconds between timestamps

    Question: Work out the number of seconds between the timestamps '2012-08-31 01:00:00' and '2012-09-02 00:00:00'

    Answer: select extract(epoch from (timestamp '2012-09-02 00:00:00' - timestamp '2012-08-31 01:00:00'))

# Work out the number of days in each month of 2012

    Question: For each month of the year in 2012, output the number of days in that month. Format the output as an integer column containing the month of the year, and a second column containing an interval data type.

    Answer: select 	extract(month from cal.month) as month,
	        (cal.month + interval '1 month') - cal.month as length from
	        ( select generate_series(timestamp '2012-01-01', timestamp '2012-12-01', interval '1 month') as month
	        ) cal order by month;  

# Work out the number of days remaining in the month

    Question: For any given timestamp, work out the number of days remaining in the month. The current day should count as a whole day, regardless of the time. Use '2012-02-11 01:00:00' as an example timestamp for the purposes of making the answer. Format the output as a single interval value.

    Answer: select (date_trunc('month', result.ts) + interval '1 month') 
		- date_trunc('day', result.ts) as remaining
	from (select timestamp '2012-02-11 01:00:00' as ts) result


# Work out the end time of booking

    Question: Return a list of the start and end time of the last 10 bookings (ordered by the time at which they end, followed by the time at which they start) in the system.

    Answer: select starttime, starttime + slots*(interval '30 minutes') endtime
            from cd.bookings
            order by endtime desc, 
            starttime desc
            limit 10

# Return a count of bookings for each month

    Question: Return a count of bookings for each month, sorted by month

    Answer: select date_trunc('month', starttime) as month, count(starttime)
            from cd.bookings
            group by month
            order by month

# Work out the utilisation percentage for each facility by month

    Question: Work out the utilisation percentage for each facility by month, sorted by name and month, rounded to 1 decimal place. Opening time is 8am, closing time is 8.30pm. You can treat every month as a full month, regardless of if there were some dates the club was not open.

    Answer: select name, month, round(

            (100*slots)/ cast( 25*(cast((month + interval '1 month') as date)
            - cast (month as date)) as numeric),1) as utilisation
            
            from  ( select facs.name as name, date_trunc('month', starttime) as month, 
            sum(slots) as slots from cd.bookings bks
            inner join cd.facilities facs on bks.facid = facs.facid
            group by facs.facid, month ) as inn
            order by name, month