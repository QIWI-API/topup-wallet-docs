# User Account Check {#check-deposit}

###### Last update: 2020-07-14 | [Edit on GitHub](https://github.com/QIWI-API/topup-wallet-doc/blob/master/_check-deposit_en.html.md)

QIWI Wallet Top-Up API provides Agent with ability to check if the user is registered in QIWI Wallet system and whether its account can be replenished.

## Request format

### Request parameters

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<request>
  <request-type>check-deposit-possible</request-type>
  <terminal-id>123</terminal-id>
  <extra name="password">XXXXX</extra>
  <extra name="phone">79031234567</extra>
  <extra name="income_wire_transfer">1</extra>
</request>
~~~

Tag|Description
----|-------
*request* | A grouping tag. The child tags contain payment parameters
*request-type* | Request type (user account check: `check-deposit-possible`)
*terminal-id* | Agent ID in QIWI Wallet system
*extra name"password"* | Agent’s password in QIWI Wallet system
*extra name="phone"* | User’s phone number. This number is used to check whether user account with the specified number is already registered in QIWI Wallet system
*extra name="ccy"* | Currency of user account balance. Optional parameter (numeric or character code of currency according  to ISO 4217), in this case QIWI Wallet system checks whether user has balance in this currency.
*extra name="income_wire_transfer"* | Integer flag to indicate the type of funds that the Agent received from the client for its wallet's replenishment - cash (0) or non-cash (1).

## Response format

### Successful request processing

~~~xml
<response>
  <result-code fatal="false">0</result-code>
  <exist>1</exist>
  <deposit-possible>1</deposit-possible>
</response>
~~~

Response data:

Tag|Description|Attributes
---|----|-------
*result-code* | [The result code](#tech_error) of the request processing | `fatal` – logical flag indicating fatal (unchanged) error.
*exist* | Integer flag indicating whether the user is registered in QIWI Wallet system. The flag is included only in case of successful request processing (error code `0`). The flag may have one of the following values:<br>`0` – the user is not registered in QIWI Wallet system (if `extra name="ccy"` tag is specified in the request, then the user does not have balance in the specified currency);<br>`1` – the user is registered in QIWI Wallet system (if `extra name="ccy"` tag is specified in the request, then the user has balance in the specified currency).|No.
*deposit-possible* | Integer flag indicating whether the user account in QIWI Wallet system can be replenished. The flag is included only in case of successful request processing (error code `0`). The flag may have one of the following values:<br>`0` – the user account cannot be replenished by specified type of funds;<br>`1` – the user account can be replenished by specified type of funds.|No.

### Error response

If QIWI Wallet server is unable to process the request, the response is as follows.

~~~xml
<response>
  <result-code fatal="false">300</result-code>
</response>
~~~

Response data:

Tag|Description|Attributes
--------|------|---------
*result-code* | Request processing error [code](#tech_error)| `fatal` – logical flag indicating if request processing error is fatal
