# Caching

Ingresso cache data at these levels:

1. Events
2. Performances
3. Availability


## Caching for events and performances

Ingresso cache two data sets for events and performances:

a) Approximately every hour Ingresso request events and performances from the
individual supplier ticketing systems - any new events or performances are
automatically added to the Ingresso database. New events are supplemented with
images, descriptive content etc by our setup team and then go live, but
performances automatically go live. When you request [events](#events) or
[performances](#performances) this request is answered directly from the
Ingresso database; we do not pass the request on to the supplier ticketing
system.

b) Every time an [availability request](#availability) is made, Ingresso
recalculate cost ranges. Cost ranges include a minimum and maximum price and
details of any offers. Since they are built from availability requests, they are
not necessarily accurate (for example if a new cheaper performance is added but
availability hasn't yet been requested for that performance, then the cost range
for the event will not be accurate. We have processes to keep these up to date
in some cases, but they are not guaranteed to be present. Cost ranges are
described in more detail for example in the [event cost range](#cost-range)
section.


## Caching for availability

Availability is cached for a short period of time (usually 90 seconds). If for
example your end customer selected the 1st March 7.30pm performance, then
selected 2nd March 7.30pm, then selected 1st March 7.30pm again at this point
the availability cache would be hit (assuming it was within 90 seconds of the
first request). This is to avoid overloading the supplier ticketing systems
during periods of high demand, for example an on-sale. The availability cache is
cleared when tickets are reserved, so the cached availability is unlikely to be
inaccurate.


## Other caching requirements

If the above caching does not solve your problem, or if you have other caching
requirements, please get in touch! api@ingresso.co.uk

Do not go away and implement your own caching system that continuously makes
availability requests to Ingresso, without discussing this with us in advance.
This puts more load than necessary on the supplier ticketing systems, and can
therefore reduce the throughput that we can offer. It is more complex than
partners often first anticipate, for example it normally takes a lot longer to
scan all performances for all events than partners imagine, and there are a
number of edge cases that are easy to miss, e.g. avoiding retrying when a
supplier system is under load or returning errors. We will consider closing the
API accounts of anyone doing this without prior approval.
