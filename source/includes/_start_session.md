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
`user_id` | Client API username provided by TicketSwitch. Your API username controls the `events` you can see, your pricing, and how you purchase tickets.
`user_passwd` | Client API password provided by TicketSwitch.

> **Response**

```http
HTTP/1.1 200 OK
Content-Type: text/xml

<start_session_result>
    <subdomain_user_is_bad>yes</subdomain_user_is_bad>
    <crypto_block>U_--6-bsjWOjlRgre-sDbJR9OBcf8hK6xqSNfGtNSPBPb61l0yAibpFTgRQ3KdZIzDisgzqts9dVHUBrNLid8ZVZ9BGd-rjzGOX57cknKbgeoljvFSpIovXIvBbSf60nZ2HwY</crypto_block>
    <running_user>
        <backend_group>demo_internal_debit_group</backend_group>
        <content_group/>
        <default_country_code>uk</default_country_code>
        <is_b2b>no</is_b2b>
        <real_name>Demonstration User</real_name>
        <statement_descriptor/>
        <style>fixed-tabs</style>
        <sub_style>styled-aff-default</sub_style>
        <sub_sub_style>ingresso-generic</sub_sub_style>
        <sub_user/>
        <user_id>demo</user_id>
    </running_user>
    <country_code>uk</country_code>
    <country_desc>United Kingdom</country_desc>
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
