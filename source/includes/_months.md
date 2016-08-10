# Months

https://www.fromtheboxoffice.com/cgi-bin/json_months.exe?event_id=2GXJ

Outout is the same as for json_performances as regards the events_by_id
block at the top, then there is a flat array of months like this:

{

    "month_date_mask": 2012209072,
    "month_day_mask": 126,
    "month_desc": "August",
    "month_number": 8,
    "year_number": 2016

},
The date mask shows which dates in the month have valid
performances, and the day mask shows which days of the week.
Bit 0 in the day maks is sunday (i.e. the above event does not
have any sunday performances), and in the date mask bit 0
represnet the firts day of the month, and so on (i.e. day number -1 is
the bit number). If you print the above in
binary you get this...

	1110111111011111101111110110000

so you can see we start on the 5th (today) and have operformances all the way
to the end of the month.

This will be extended to oprovide the usual cost ranges.