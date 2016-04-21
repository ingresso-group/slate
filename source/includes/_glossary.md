#Glossary


###Events

Show or attraction in a specific venue. ex. *Lion King at the O2
Arena*

###Performance

Specific show on a specific date and time. ex. *Lion King at the
O2 Arena on Saturday 24th of September at 7:30pm*

###Backend System

Ticketing System or system TicketSwitch connects to to get,
reserve and purchase tickets.

###Venue

Ex. National Theatre, XYZ Arena, Globe Theatre, etc.

###Area

It's a just collection of venues. It could be an area within a
city, a city, a country, etc.

###Ticket Type

ex. Dress Circle, Front Stalls.

###Price Band

Price group within Ticket Types. *Ex. £34 + surcharge. Price band
Id is called Band\_Token*

###user\_id

Client API's username provided by TicketSwitch. It's unique per
Client and has its own setup and products that are allow to see
and buy.

###user\_passwd

Client API's password provided by TicketSwitch. see user\_id

###Products

Any of the events that are available through the
TicketSwitch platform.

###CryptoBlock

It's token for authentication and information passing between
clients and TicketSwitch. CryptoBlock are encrypted and should not
be altered in any way.

<aside class="warning">
CryptoBlocks are provided by the system as part of a flow. They change on every step of each flow
</aside>

###Available Events

Events that are available for booking. Because of the nature of
the industry TicketSwitch could return (on search) events that are
not available. i.e. sold out by the venue in the last minute.

###Client

*(Also API Client)* Any company/individual that uses TicketSwitch
systems through our API to search and sell tickets.

###Price Band

Collection of seats with the same price setup by the Backend
System

###Ticket Type

The Class of ticket one could buy. Ex. for thearte would be type
of seats: i.e. stall. upper circle, Tour with Lunch, Tour without
Lunch, etc.

###Event Token

Event Identifier.

###Band Token

A token to identify the event, performance, ticket type and price
band for the purposes of creating an order.

###Supplier

Organisations that run enterntainment events and offer tickets to
the public and Ticketing Agent. Examples: Theatre Groups,
Independent Venues, Tour Providers, etc.

###Allocation

Tickets are assigned exclusively to a Ticketing Agent to be sold.

###Ticket Agent

Organisation responsible for selling and update inventory
of tickers.

###Ticketing System

Software the Suppliers use to manage their inventory of tickets
usually with other functions they need to run their business:
accounting, reporting, etc.
