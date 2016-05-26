#Glossary

###Ticketing System

Software solution that is used by ticket suppliers to manage their inventory of tickets. This is commonly provided by a third party software company (examples include Tessitura, Outbox, Enta, Spektrix), but some organisations have developed their own ticketing system (for example Viator, Ticket Zone and Ticketmaster).

###Backend System

A Ticketing System instance that TicketSwitch connects to in order to view, reserve and purchase tickets.

###Supplier

Organisations that run enterntainment events and offer tickets to
the public and Ticketing Agent. Examples: Theatre Groups,
Independent Venues, Tour Providers, etc. 
Often a supplier is represented by a single backend system, but sometimes a backend system can contain products from multiple suppliers.

###Allocation

Tickets are assigned exclusively to a Ticketing Agent to be sold.

###Ticket Agent

Organisation involved in selling tickets.

###Venue

Ex. National Theatre, O2 Arena, Globe Theatre, etc.

###Area

It's a just collection of venues. It could be an area within a
city, or a city, or a country, etc. This isn't an important concept for distribution channels to understand.

###Event

Show or attraction in a specific venue. ex. *Lion King at the Lyceum Theatre* or *Justin Bieber at the O2
Arena*

###Performance

Specific show on a specific date and time. ex. *Lion King at the
Lyceum Theatre on Saturday 24th of September at 7:30pm*

###Products

Any of the events that are available through the
TicketSwitch platform.

###CryptoBlock

It's token for authentication and information passing between
clients and TicketSwitch. CryptoBlock are encrypted and should not
be altered in any way.

<aside class="warning">
CryptoBlocks are provided by the system as part of a flow. They change on every step of each flow.
</aside>

###Available Events

Events that are available for booking. Because of the nature of
the industry TicketSwitch could return (on search) events that are
not available. i.e. sold out by the venue in the last minute.

###Client

*(Also API Client)* Any company/individual that uses TicketSwitch
systems through our API to search and sell tickets.

###Ticket Type

The classification of ticket. For example Stalls or Upper Circle for theatre, or Tour with Lunch or Tour without Lunch for attractions, etc.

###Price Band

Collection of seats with the same price setup by the Backend System. A collection of seats that have the same price within a Ticket Type. *Ex. £35 tickets within the Upper Circle. Price band
Id is called Band\_Token*

###Band Token

A token to identify the event, performance, ticket type and price band for the purposes of creating an order.

###Event Token

Event Identifier.

###user\_id

Client API's username provided by TicketSwitch. It's unique per
Client and has its own setup and products that are allow to see
and buy.

###user\_passwd

Client API's password provided by TicketSwitch. see user\_id