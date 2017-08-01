# Balance Check Request {#get-balance}

###### Last update: 2017-07-27 | [Edit on GitHub](https://github.com/QIWI-API/topup-wallet-doc/blob/master/_get-balance_ru.html.md)

Agent can check the balance of the Agent’s account in Visa QIWI Wallet system at any time using balance check request.

## Request format

~~~xml
<request>
<request-type>ping</request-type>
<terminal-id>44</terminal-id>
<extra name="password">password</extra>
</request>
~~~

### Request parameters

Parameter|Description
-|-
*request*| A grouping tag
*request-type* | Request type (balance check: `ping`)
*terminal-id* | Agent ID in Visa QIWI Wallet system
*extra name="password"* | Agent’s password in Visa QIWI Wallet system

## Response format

~~~xml
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
*balances*|A grouping tag. The child tags are current balances of the Agent's active accounts in Visa QIWI Wallet system |No
*balance* | Balance of the Agent's account| `code` - currency of the Agent's account (numeric code of currency according  to ISO 4217)
