# User Availability Check {#check-user}

###### Last update: 2017-07-27 | [Edit on GitHub](https://github.com/QIWI-API/topup-wallet-doc/blob/master/_check-user_en.html.md)
	
Visa QIWI Wallet provides Agent with ability to check if the user is registered in Visa QIWI Wallet system. User availability is not a requirement for a payment registration. If the specified user account does not exist, it will be created upon top-up request.

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
*terminal-id* | Agent ID in Visa QIWI Wallet system
*extra name"password"* | Agent’s password in Visa QIWI Wallet system
*extra name="phone" | User’s phone number. This number is used to check whether user account with the specified number is already registered in Visa QIWI Wallet system
*extra name="ccy" | Currency of user account balance. Optional parameter (numeric or character code of currency according  to ISO 4217), in this case Visa QIWI Wallet system check whether user has balance in this currency.

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
*exist* | Flag indicating whether the user is registered in Visa QIWI Wallet system. The flag is included only in case of successful request processing (error code `0`). The flag may have one of the following values:<br><ul><li>`0` – the user is not registered in Visa QIWI Wallet system;</li><li>`1` – the user is registered in Visa QIWI Wallet system.</li></ul><br>If request contains currency parameter (<extra name="ccy"> tag), the flag may have one of the following values:<br><ul><li>`0` – the user does not have balance in this currency;</li><li>`1` – the user has balance in this currency.</li></ul>|No.

