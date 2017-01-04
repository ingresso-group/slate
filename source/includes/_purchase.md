# Purchase

General recommendation on sending customer data:

* You must pass in the customer name, phone numbers and address for the purchase
to succeed, and in some cases the email address is also required. Some partners
do not wish to share customer data. At a minimum you should send us the customer
name (first and last name) and phone number.

* The customer name is used by the venue, in particular as a security measure 
when admitting customers, so this is essential.

* The phone number can be used by Ingresso or the venue in emergency
circumstances to contact the customer (for example if an evening performance is
cancelled in the morning). It will not be used for any other reason. We
therefore highly recommend that you provide the customer's phone number to
avoid a bad customer experience should they need to be contacted. We provide two
phone number fields - most partners send a single phone number in both fields,
but you can pass both phone numbers.

* The email address is only required if `needs_email_address` = `true` in the
response from [reserve](#reserve). We recommend that you always send us the
customer's email address. We guarantee to only use this if our customer service
team need to contact your customer or if our supplier needs to directly email
confirmation to your customer (this is not used for any events as at Jan 2017,
but is possible in future). If you cannot send the customer's email address then
we recommend that you don't send an email address if `needs_email_address` is
`false`; if it is `true` then send an internal email address (for example your
customer service email address) and it will be your responsibility to contact
the customer or pass on booking confirmation.

* The address fields (for example `address_line_one`) need to be passed to us.
The only use for these is for tickets that will be posted by Ingresso or the
supplier, so it is possible to only pass the customer's address when the
customer has selected a despatch method with `send_type` = `post`. In all other
cases you can pass a default address such as your company headquarters (in case
it turns out that tickets need to be unexpectedly posted).
