# User Availability Check {#check-user}

###### Last update: 2017-11-15 | [Edit on GitHub](https://github.com/QIWI-API/topup-wallet-doc/blob/master/_check-user_en.html.md)

QIWI Wallet Top-Up API provides Agent with ability to check if the user is registered in QIWI Wallet system. User availability is not a requirement for a payment registration. If the specified user account does not exist, it will be created upon top-up request.

## Request format

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<request>
  <request-type>check-user</request-type>
  <terminal-id>123</terminal-id>
  <extra name="password">XXXXX</extra>
  <extra name="phone">79031234567</extra>
  <extra name="ccy">RUB</extra>
</request>
~~~

### Request parameters

Tag|Description
----|-------
*request* | A grouping tag. The child tags contain payment parameters
*request-type* | Request type (user availability check: `check-user`)
*terminal-id* | Agent ID in QIWI Wallet system
*extra name"password"* | Agent’s password in QIWI Wallet system
*extra name="phone"* | User’s phone number. This number is used to check whether user account with the specified number is already registered in QIWI Wallet system
*extra name="ccy"* | Currency of user account balance. Optional parameter (numeric or character code of currency according  to ISO 4217), in this case QIWI Wallet system checks whether user has balance in this currency.

## Response format

~~~xml
<response>
  <result-code fatal="false">0</result-code>
  <exist>1</exist>
</response>
~~~

Response data:

Tag|Description|Attributes
---|----|-------
*result-code* | [The result code](#tech_error) of the request processing | `fatal` – logical flag indicating fatal (unchanged) error.
*exist* | Flag indicating whether the user is registered in QIWI Wallet system. The flag is included only in case of successful request processing (error code `0`). The flag may have one of the following values:<br>`0` – the user is not registered in QIWI Wallet system (if `extra name="ccy"` tag is specified in the request, then the user does not have balance in the specified currency);<br>`1` – the user is registered in QIWI Wallet system (if `extra name="ccy"` tag is specified in the request, then the user has balance in the specified currency).|No.

