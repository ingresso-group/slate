## Start Session

This method validates username/password. It can be used to `ping` TicketService.

<aside class="notice">
This call is optional and it's not part of any flow.
</aside>

> **Request**

```http
POST https://api.ticketswitch.com/tickets/xml_core.exe HTTP/1.1
User-Agent: MyClient/1.0.0
Content-Type: text/xml
Accept: text/xml

<start_session>
<user_id>demo</user_id>
<user_passwd>demopass</user_passwd>
</start_session>
```

> Make sure to replace `demo` with your user id, and `demopass` with your
> password.


###Request

Parameter | Description
--------- | -----------
`user_id` | Client API's username provided by TicketSwitch. It's unique per Client and has its own setup and `products` that are allow to see and buy.
`user_passwd` | Client API's password provided by TicketSwitch.

> **Response**

```http
HTTP/1.1 200 OK
Content-Type: text/xml


<start_session_result>
  <subdomain_user_is_bad>no</subdomain_user_is_bad>
  <crypto_block>M_--qRfq64CDfM9Vj4JBQdRMdOJPW0xEpX1WsPqJY6Sy_XkrL_G8x-0i4sQu1cKhPNF-2PkEozP_rOW8KEiQSB10ZOO5hkKzT1OUz6IdkZ5DnW4DXoVa_AwIc0--Y</crypto_block>
  <running_user>
    <user_id>demo</user_id>
  </running_user>
</start_session_result>
```

###Response

Parameter | Description
--------- | -----------
`crypto_block` | User session state. 
`running_user` | Information about the authorised user. (See Below)


###Running User Parameters

Parameter | Description
--------- | -----------
`user_id` | The user id the script is running under
