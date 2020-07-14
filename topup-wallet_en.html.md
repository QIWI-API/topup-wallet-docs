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
2. Check the user registration in QIWI Wallet service - [check-user](#check-user).
3. Monitor the agent's balance - [ping](#get-balance).

## How it works

1. A user provides you a QIWI Wallet account and money amount to top-up.
2. You send a [request](#check-deposit) to QIWI Wallet Top-Up API to check if the user is registered in QIWI Wallet system and whether its account can be replenished.
3. You send a [request](#payment) to QIWI Wallet Top-Up API to top-up the account.
4. You [check](#status) the payment's current status till it becomes final.
5. On successful status, money is transferred from your agent's account to the user's QIWI Wallet account.
6. On unsuccessful status, money is returned to the user.

## Feedback

Please contact us on <a href="mailto:bss@qiwi.com">bss@qiwi.com</a> for integration and cooperation.

# API Structure {#general}

The Agent’s system uses HTTPS protocol to send POST requests to

`https://api.qiwi.com/xml/topup.jsp`

All requests and responses are XML documents encoded in UTF-8 in HTTP body. 

<aside class="warning">All tags and attributes of the requests described in the document are required (if not specified otherwise for a certain tag/attribute). It is not an API contract violation that any other tags/attributes might be added in responses or requests. Check for updates of the protocol on the Github page</aside>

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

<div class="mermaid">
sequenceDiagram
participant Agent
participant qb as QIWI Wallet Service
activate Agent  
activate qb  
Agent->>qb:Check client registration<br>and ability to top up<br>("check-deposit-possible" request)
qb->>Agent:Response
deactivate Agent 
deactivate qb  
alt Wallet can be replenished
Agent->>qb:Request to top-up client's account<br>("pay/auth" request)
activate Agent  
activate qb  
qb->>qb:Creating wallet <br>when no account is found
qb->>Agent:Response (payment status or error)
Agent->>Agent:Final status?
alt If final status not received
loop Cycle until receiving final status
Agent->>qb:Check top-up payment status<br>("pay/status" request)
qb->>Agent:Response with current payment status
end
end
alt Final successful status
qb->>qb:Funds top-up to client's wallet
deactivate qb
end
alt Final unsuccessful status
Agent->>Agent:Return funds to Client
end
deactivate Agent
end
</div>

<!--![Top-up logic to be implemented by the Agent’s system](/images/topup_flow_en.png)-->

<!-- 
The Agent system should implement the logic shown in the flowchart below. Some details are omitted from the flowchart for readability, particularly retrying requests to QIWI Wallet in case of non-fatal error.

![Interaction between Agent and QIWI Wallet](/images/topup_en.png)
-->
