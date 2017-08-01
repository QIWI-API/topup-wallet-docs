# Payment Statuses {#statuses}

###### Last update: 2017-07-26 | [Edit on GitHub](https://github.com/QIWI-API/topup-wallet-doc/blob/master/_errors_en.html.md)

Visa QIWI Wallet server may return payment status from the following ranges:

Status | Description | Final?
-----|---------|-------------
\-1	| The payment is not registered due to temporary error. Please repeat the request later.|
0\-49|The payment is accepted but waiting for the confirmation from Visa QIWI Wallet system. Contact Visa QIWI Wallet technical support: <a href="mailto:bss@qiwi.com">bss@qiwi.com</a> | \-
50\-59	| The payment is being processed. The amount has been charged from the Agent’s account.| \-
50|The payment is accepted for processing| \-
52|The amount is being credited to the user account| \-
60|The payment has been processed successfully| \+
\> 100|Processing error. The amount has been put back to the Agent’s balance| \+
150|The payment declined|\+
151|The payment authorization error|\+
160|The payment is not processed or has been canceled|\+

Error codes for unsuccessful final states are listed in [payment processing results](#error).

# Payment Processing Result Codes {#error}

Error code| Error description
-----|--------
0| No errors
155|Invalid service code (`service-id` in top-up request should be `99`)
215| Top-up request has payment transaction number (`transaction-number`) that is already registered in Visa QIWI Wallet but other parameters differ. Payment parameters have to be in agreement with this payment transaction number.
220|Not enough funds available on the Agent’s account to process payment
241|Payment amount is less than allowed
242|Payment amount is greater than allowed
298|User account with specified phone number is not registered in Visa QIWI Wallet system. Invalid phone number as user account ID.
300|Unknown processing error. Contact Visa QIWI Wallet technical support: <a href="mailto:bss@qiwi.com">bss@qiwi.com</a>
316|Authorization from the blocked agent
319|Top-up of this user account is blocked
700|Monthly limit on operations is exceeded 
702|Visa QIWI Wallet client’s account balance limit is exceeded

If there are errors appeared that is not described in this table please contact Visa QIWI Wallet technical support: <a href="mailto:bss@qiwi.com">bss@qiwi.com</a>.

# Visa QIWI Wallet Error Codes {#tech_error}

Error code|Description|Fatal?
----|------|---------
0|No errors|Inapplicable
13|Repeat the request in a minute|\-
150|Authorization error. Make sure that login and password are correct and repeat the request |\+
300|Unknown error. Repeat the request|\-
339|Sender IP address blocked|\+

If there are errors appeared that is not described in this table please contact Visa QIWI Wallet technical support: <a href="mailto:bss@qiwi.com">bss@qiwi.com</a>.

# XML Data Format {#params-types}

Tag/attribute|Data type
--------|----------
`request-type`|Text with letters only
`terminal-id`|Integer positive
`transaction-number`|Integer positive up to 20 digits
`amount`|Decimal (with two fractional digits separated by point)
`to/service-id`	| Constant (99)
`from/service-id`|Integer positive
`account-number`|International phone number (without leading `+` sign)
`final-status`|Logical (`true/false`)
`fatal-error`|Logical (`true/false`)
`txn-date`| Timestamp formatted as:<br>`dd.MM.yyyy HH:mm:ss`
`balance`|Decimal (with two fractional digits separated by point)
`fatal`|Logical (`true/false`)
`exist`|`0/1`
`"password"`|Text
`"comment"`|Text (up to 1000 symbols)
`"phone"`|International phone number (without leading `+` sign)




