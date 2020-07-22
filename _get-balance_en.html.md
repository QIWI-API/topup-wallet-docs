# Balance Check Request {#get-balance}

###### Last update: 2017-11-14 | [Edit on GitHub](https://github.com/QIWI-API/topup-wallet-doc/blob/master/_get-balance_ru.html.md)

This API method helps Agent to check the balance of the Agent’s account in QIWI Wallet system.

## Request format

### Request parameters

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<request>
<request-type>ping</request-type>
<terminal-id>44</terminal-id>
<extra name="password">password</extra>
</request>
~~~

Parameter|Description
-|-
*request*| A grouping tag
*request-type* | Request type (balance check: `ping`)
*terminal-id* | Agent ID in QIWI Wallet system
*extra name="password"* | Agent’s password in QIWI Wallet system

## Response format

### Successful request processing

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<response>
<result-code fatal="false">0</result-code>
<balances>
  <balance code="428">100.00</balance>
  <balance code="643">200.26</balance>
  <balance code="840">300.00</balance>
</balances>
</response>
~~~

Response data:

Tag|Description|Attributes
--------|------|---------
*response*| A grouping tag|No
*result-code* | [The result code](#tech_error) of the request processing | `fatal` – logical flag indicating fatal (unchanged) error.
*balances*|A grouping tag. The child tags are current balances of the Agent's active accounts in QIWI Wallet system |No
*balance* | Balance of the Agent's account| `code` - currency of the Agent's account (numeric code of currency according  to ISO 4217)

### Error response

If QIWI Wallet server is unable to process the request, the response is as follows.

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<response>
  <result-code fatal="false">300</result-code>
</response>
~~~

Response data:

Tag|Description|Attributes
--------|------|---------
*result-code* | Request processing error [code](#tech_error)| `fatal` – logical flag indicating if request processing error is fatal
