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
 - topup-wallet/check-deposit_en
 - topup-wallet/check-user_en
 - topup-wallet/get-balance_en
 - topup-wallet/errors_en

---

# Introduction {#introduction}

###### Last update: 2020-07-10 | [Edit on GitHub](https://github.com/QIWI-API/topup-wallet-doc/blob/master/topup-wallet_en.html.md)

Top-Up API is intended for merchants or payment service providers (PSP) who need to send money to QIWI Wallet users.

## What API allows you

1. Send money to QIWI Wallet users - [pay](#payment).
2. Check the current status of the top-up payment - [status](#status).
3. Check the payment possibility - [check-deposit-possible](#check-deposit).
4. Check the user registration in QIWI Wallet service - [check-user](#check-user).
5. Monitor the agent's balance - [ping](#get-balance).

## How it works

1. A user provides you a QIWI Wallet account and money amount to top-up.
2. You send a [request](#check-deposit) to QIWI Wallet Top-Up API to check payment possibility.
3. You send a [request](#payment) to QIWI Wallet Top-Up API to top-up the account.
4. You [check](#status) the payment's current status until it becomes final.
5. On successful status, money is transferred from your agent's account to the user's QIWI Wallet account.
6. On unsuccessful status, money is returned to the user.

## Feedback

Please contact us on <a href="mailto:bss@qiwi.com">bss@qiwi.com</a> for integration and cooperation.

# API Structure {#general}

All requests and responses are XML documents encoded in UTF-8 in HTTP body. 

<aside class="warning">All tags and attributes of the requests described in the document are required (if not specified otherwise for a certain tag/attribute). It is not an API contract violation that any other tags/attributes might be added in responses or requests. Check for updates of the protocol on the Github page</aside>

See [Values Format](#params-types) of XML tags and attributes.

Agent uses [Agent ID and password](#agent_account) to authenticate API requests. Place Agent ID in `terminal-id` and password in `extra name="password"` XML tags, respectively. To change password, contact QIWI Wallet staff. 

To improve the security of information exchange, the Agent may also use [client certificate](#cert) and [digital signature](#sign) methods to authenticate requests.

The Agent’s system in production environment must use HTTPS protocol and send only POST requests to URL

`https://api.qiwi.com/xml/topup.jsp`

When using [client certificate](#cert) for requests authentication, the Agent’s system in production environment must use HTTPS protocol and send only POST requests to URL

`https://private-api.qiwi.com/xml/topup.jsp`

Agent must make decision on successful or unsuccessful payment processing based on transaction status in QIWI Wallet system.

Agent must assign a unique identifier for each payment completely characterized by a set of parameters: amount, currency, QIWI Wallet user account, service identifier.

See section [Implementation Guidelines](#guidelines) for the algorithm to be implemented at the Agent’s side.

## Agent Account {#agent_account}

To use the API, the Agent should set up an agent’s account with QIWI Wallet. The amounts credited to user accounts are taken from the Agent’s account balance. The Agent's credentials (Agent ID and password) are provided accordingly.

## QIWI Wallet User Accounts

QIWI Wallet identifies its users by mobile phone number.

To top up user account in QIWI Wallet, the Agent specifies user’s mobile phone number and the amount in a top-up request.

# Implementation Guidelines {#guidelines}

The scenario of the QIWI Wallet top-up should go as follows:

<div class="mermaid">
sequenceDiagram
participant Agent
participant qb as QIWI Wallet Service
Agent->>qb:Check payment possibility<br>("check-deposit-possible" request)
qb->>Agent:Response
alt Payment is possible
Agent->>qb:Request to top-up client's account<br>("pay/auth" request)
qb->>qb:Creating wallet <br>when no account is found
qb->>Agent:Response (payment status or error)
Agent->>Agent:Final status?
alt If final status not received
loop Cycle until receiving final status
Agent->>qb:Check top-up payment status<br>("pay/status" request)
qb->>Agent:Response with current payment status
end
end
alt Success / Final
qb->>qb:Funds top-up to client's wallet
end
alt Fail / Final
Agent->>Agent:Return funds to Client
end
end
</div>

The Agent's system should implement the logic shown in the flowchart below.

<div class="mermaid">
graph TB
    ST(BEGIN QIWI WALLET TOP-UP) -.- A
    A[Check Payment Possibility Request] --> B{Payment<br>is possible?}
    B -->|yes| C{User ID exists??}
    B -->|no| G[[Payment NOT processed]]
    C -->|yes| K
    C -->|no| H{Create user<br>and register payment?}
    H -->|yes| K[QIWI Wallet Top-up Request]
    H -->|no, if no such user, then<br>do not process payment| G
    K -->KN{Network error?}
    KN & KER -->|yes| KW([Wait 10 minutes or more])
    KER -->|no| PFS
    KN -->|no| KER{Request processing error?}
    PFS -->|final-status=true| RS{'status' attribute value?}
    PFS -->|final-status=false| MR
    KW --> MR[Payment Status Check Request]
    RS -->|> 100| G
    RS -->|=60| X[[Payment processed successfully]]
    MR --> MER{Network error?}
    MER -->|no| MW{Request processing error?}
    MW & MER -->|yes| KW
    MW -->|no| PT{Is there 'payment' tag<br>with the 'txn_id' transaction number<br>in response?}
    PT -->|yes| PFS{'final-status' attribute?}
    PT -->|no| KW
</div>
