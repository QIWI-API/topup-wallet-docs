---
title: QIWI Wallet Top-Up API

search: true

metatitle: QIWI Wallet Top-Up API

metadescription: Top-Up API is intended for merchants or payment service providers (PSP) who need to send money back to Visa QIWI Wallet users.

language_tabs:
  - xml: XML

toc_footers:
 - <a href='/'>Main page</a>
 - <a href='mailto:api_help@qiwi.com'>Feedback</a>

includes:
 - topup/topup_en
 - topup/topup-status_en
 - topup/check-user_en
 - topup/get-balance_en
 - topup/authorization_en
 - topup/statuses_en
 - topup/errors_en

---

# Introduction {#introduction}

###### Last update: 2017-07-27 | [Edit on GitHub](https://github.com/QIWI-API/topup-wallet-doc/blob/master/topup_en.html.md)

API выплат на кошельки предназначено для платежных агентов КИВИ Банк (АО), позволяет зачислять деньги на кошельки пользователей (балансы учетных записей клиентов в системе Visa QIWI Кошелек).

## Что позволяет протокол

1. Переводить деньги клиентам Visa QIWI Кошелек - [pay](#payment).
2. Проверять существование клиентов в сервисе Visa QIWI Кошелек - [check-user](#check-user).
3. Следить за балансом на агентском счете для выплат - [ping](#get-balance).

## Как это работает

1. Пользователь передает вам сумму пополнения и номер Visa QIWI Кошелька, который нужно пополнить.
2. Вы отправляете запрос в Top-Up API QIWI Кошелька на пополнение этого кошелька.
3. Вы проверяете текущий статус платежа. Платеж должен принять финальный статус. 
4. При успешном финальном статусе платежа средства автоматически перечисляются на баланс кошелька пользователя с вашего агентского счета.
5. При неуспешном финальном статусе платежа вы возвращаете средства пользователю.

По вопросам интеграции и сотрудничества пишите на <a href="mailto:bss@qiwi.com">bss@qiwi.com</a>.

# API Structure {#general}

The Agent’s system uses HTTPS protocol to send POST requests to 

`https://api.qiwi.com/xml/topup.jsp`

All requests and responses are XML documents encoded in UTF-8 in HTTP body. All tags and attributes of the requests in the document are necessary to be present in requests (if not specified otherwise for a certain tag/attribute). It is not an API contract violation that any other tags/attributes might be added in responses or requests.

See [Values Format](#params-types) of XML tags and attributes.

Agent must make decision on successful or unsuccessful payment processing based on transaction status in Visa QIWI Wallet system.

Agent must assign a unique identifier for each payment completely characterized by a set of parameters: amount, currency, Visa QIWI Wallet user account, service identifier.

Agent will be notified if any update of the API is taken place. 

See section Implementation Guidelines for the algorithm to be implemented at the Agent’s side.

## Agent Account

To use the API, the Agent should set up an agent’s account with Visa QIWI Wallet. The amounts credited to user accounts are taken from the Agent’s account balance. 

The Agent uses Agent ID and password to authenticate requests to the API. To change password, contact Visa QIWI Wallet staff. The Agent may also use [client certificate](#cert) and [digital signature](#sign) methods to authenticate requests.

## Visa QIWI Wallet User Accounts

Visa QIWI Wallet identifies its users by mobile phone number. 

To top up user account in Visa QIWI Wallet, the Agent specifies user’s mobile phone number and the amount in a top-up request.

# Implementation Guidelines

The interaction of the Agent’s system with Visa QIWI Wallet should go as follows.

![Top-up logic to be implemented by the Agent’s system](images/topup_flow.jpg)

<aside class="notice">Client availability check is not necessary for payment registration. When payment is registered, client is created automatically when absent in Visa QIWI Wallet system.</aside>

The Agent system should implement the logic shown in the flowchart below. Some details are omitted from the flowchart for readability, particularly retrying requests to Visa QIWI Wallet in case of non-fatal error.

![Interaction between Agent and Visa QIWI Wallet](images/topup_en.jpg)
