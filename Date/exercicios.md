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

    Answer: 