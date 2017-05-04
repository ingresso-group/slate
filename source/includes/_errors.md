# Errors

> **Example**

```shell
HTTP/1.1 400 Bad Request

{
  "error_code": 8,
  "error_desc": "Bad data supplied"
}
```

```python
APIError(
    msg='Bad data supplied',
    code=8,
    response=<Response [400]>
)

```

When an error happens the API will return a non `2XX` HTTP status code and a
message about the error in the response body. The message will include a
human readable description of the error and a code that can be used by
application to recognise the issue and handle it appropriately.

Some method calls can have partial failures. For example a reservation call
might be successful for one order in the trolley but fail to reserve a second
order, or a purchase call might be successful in that we successfully
attempted a payment but the users card was declined, or the backend system
fell over mid transaction. In these situations the API will return a 200 HTTP
status code with details of the issues in the response body.  You should
consult the documentation for the specific end point for details on
how to handle these situations.


## Status codes

Details of the general HTTP status codes can be found below:

Status Code | Description
------------|------------
`200 - OK` | The call was successful and the response can be interpreted a documented.
`400 - Bad Request` | Your call was missing data required to satisfy the request. This response is generally occurs due to missing parameters.
`401 - Unauthorised` | No authentication parameters were provided or the given parameters were invalid.
`403 - Forbidden` | You are trying to access something your account doesn't have access to.
`404 - Not Found` | Then endpoint you were trying to reach does not exist.
`405 - Method Not Allowed` | you have tried to use a POST request when the endpoint was expecting a GET request (or visa versa).
`410 - Gone` | The resource you were trying to access no longer exists. This is primarily used around transactions when temporary resources (such as callouts) have expired.
`460 - Invalid Parameters` |  The parameters passed to the API were technically valid, but where rejected due to their content. Refer to the error description and error code for further details.
`5XX - Server Errors` | Something unexpectedly went wrong and the API was unable to do what you asked it to do. You should refer to the error code/description in the message body for further details.

One specific case that is worth mentioning in detail is when the API returns a
502 HTTP status code. This indicates that your request required a connection to an
upstream server (for example the target theatre, or a payment provider), but
we were unable to establish the connection. A lot of the systems that our API
connects to are very fragile; we do what we can to mitigate this, but
sometimes those upstream servers simply won't be available to service your
request. In these situations you could attempt to retry the request in case it
was just momentary issue, failing that waiting a short time may help. Both
scheduled and unscheduled major outages will be recorded on our [status
page](https://ingresso.statuspage.io/).

## Error codes

Error codes provide a computer readable classifier of an error. They will be
useful in identifying errors and handling them appropriately.

Error Code | Description
-----------|-------------
`2` | Bad channel.
`3` | User authentication failure.
`4` | Failed to create connection to the backend system.
`5` | Host is on a forbidden network.
`6` | Failed to connect to database.
`7` | Membership authentication failed.
`8` | Bad data supplied.
`2000` | Email address is invalid. This error will also provide a secondary `error_key` field that will provide a code for working out exactly what's wrong with the email address (see below).
`3000` | Unrecognised card type from number.
`3001` | Card type not accepted.
`3002` | Not a valid card number.
`3003` | Invalid expiry date.
`3004` | Invalid CV2.
`3005` | Missing issue number.
`3006` | Invalid issue number.
`3007` | Missing start date.
`3008` | Invalid start date.


## Email error keys

Email error keys break down the `2000` error code into more explicit issues.

Key | Description
----|------------
`addr_dot_before_at_must_be_quoted` | A '.' before the '@' sign should be quoted
`addr_cannot_end_in_dot` | Address cannot end in '.'
`addr_cannot_have_dot_immediately_after_at` | Cannot have '.' immediately after '@' sign
`addr_cannot_have_dot_dot_after_at` | Cannot have '..' after '@' sign
`addr_cr_within_quotes` | Carriage return within quotes
`addr_cr_not_allowed_after_at` | Carriage returns not allowed after '@' sign
`addr_may_not_be_blank` | Email addresses may not be blank
`addr_may_not_end_with_at` | Email addresses may not end with '@' sign
`addr_may_not_start_with_at` | Email addresses may not start with '@' sign
`addr_missing_at` | Missing '@' sign
`addr_needs_at_least_one_dot` | Must have at least one '.' after '@' sign
`addr_spaces_before_at_must_be_quoted` | Spaces before '@' sign must be quoted
`addr_spaces_not_allowed_after_at` | Spaces not allowed after '@' sign
`addr_tabs_not_allowed_after_at` | Tabs not allowed after '@' sign
`addr_unterminated_quote_before_at` | Unterminated quote before '@'
`addr_non_ascii_in_quotes` | Non-ASCII character in quotes
`addr_dot_expected_after_quotes` | A '.' is expected after quotes
`addr_non_ascii_before_at` | Non-ASCII character before '@' sign
`addr_control_character_before_at` | Control character before '@' sign
`addr_specials_before_at_must_be_quoted` | Special characters before '@' sign must be quoted
`addr_non_ascii_after_at` | Non-ASCII character after '@' sign
`addr_control_character_after_at` | Control character after '@' sign
`addr_specials_after_at` | Special characters not allowed after '@' sign
`addr_bad_email_domain` | This domain is not e-mailable
