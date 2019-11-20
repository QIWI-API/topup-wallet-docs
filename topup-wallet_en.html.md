---
title: QIWI Wallet Top-Up API

search: true

metatitle: QIWI Wallet Top-Up API

metadescription: Top-Up API is intended for merchants or payment service providers (PSP) who need to send money back to their customers' QIWI Wallets.

language_tabs:
  - xml: Examples

toc_footers:
 - <a href='/en/'>Home page</a>
 - <a href='mailto:bss@qiwi.com'>Feedback</a>

includes:
 - topup-wallet/authorization_en
 - topup-wallet/topup_en
 - topup-wallet/topup-status_en
 - topup-wallet/check-user_en
 - topup-wallet/get-balance_en
 - topup-wallet/errors_en

---

# Introduction {#introduction}

###### Last update: 2017-11-15 | [Edit on GitHub](https://github.com/QIWI-API/topup-wallet-doc/blob/master/topup_en.html.md)

Top-Up API is intended for merchants or payment service providers (PSP) who need to send money back to QIWI Wallet users.

## What API allows you

1. Send money to QIWI Wallet users - [pay](#payment).
2. Check the user registration in QIWI Wallet service - [check-user](#check-user).
3. Monitor the agent's balance - [ping](#get-balance).

## How it works

1. A user provides you a QIWI Wallet account and money amount to top-up.
2. You send a [request](#payment) to QIWI Wallet Top-Up API to top-up the account.
3. You [check](#status) the payment's current status till it becomes final.
4. On successful status, money is transferred from your agent's account to the user's QIWI Wallet account.
5. On unsuccessful status, money is returned to the user.

## Feedback

Please contact us on <a href="mailto:bss@qiwi.com">bss@qiwi.com</a> for integration and cooperation.

# API Structure {#general}

The Agent’s system uses HTTPS protocol to send POST requests to

`https://api.qiwi.com/xml/topup.jsp`

All requests and responses are XML documents encoded in UTF-8 in HTTP body. All tags and attributes of the requests in the document are necessary to be present in requests (if not specified otherwise for a certain tag/attribute). It is not an API contract violation that any other tags/attributes might be added in responses or requests.

See [Values Format](#params-types) of XML tags and attributes.

Agent uses [Agent ID and password](#agent_account) to authenticate requests to the API (in `terminal-id` and `extra name="password"` XML tag, respectively). To change password, contact QIWI Wallet staff. The Agent may also use [client certificate](#cert) and [digital signature](#sign) methods to authenticate requests.

Agent must make decision on successful or unsuccessful payment processing based on transaction status in QIWI Wallet system.

Agent must assign a unique identifier for each payment completely characterized by a set of parameters: amount, currency, QIWI Wallet user account, service identifier.

Agent will be notified if any update of the API is taken place.

See section [Implementation Guidelines](#guidelines) for the algorithm to be implemented at the Agent’s side.

## Agent Account {#agent_account}

To use the API, the Agent should set up an agent’s account with QIWI Wallet. The amounts credited to user accounts are taken from the Agent’s account balance. The Agent's credentials (Agent ID and password) are provided accordingly.

## QIWI Wallet User Accounts

QIWI Wallet identifies its users by mobile phone number.

To top up user account in QIWI Wallet, the Agent specifies user’s mobile phone number and the amount in a top-up request.

# Implementation Guidelines {#guidelines}

The interaction of the Agent’s system with QIWI Wallet should go as follows.

![Top-up logic to be implemented by the Agent’s system](/images/topup_flow_en.png)

<aside class="notice">Client availability check is not necessary for payment registration. When payment is registered, client is created automatically when absent in QIWI Wallet system.</aside>

The Agent system should implement the logic shown in the flowchart below. Some details are omitted from the flowchart for readability, particularly retrying requests to QIWI Wallet in case of non-fatal error.

![Interaction between Agent and QIWI Wallet](/images/topup_en.png)
