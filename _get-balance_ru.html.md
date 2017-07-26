# Запрос баланса контрагента {#get-balance}

###### Последнее обновление: 2017-07-11 | [Редактировать на GitHub](https://github.com/QIWI-API/topup-wallet-doc/blob/master/_get-balance_ru.html.md)

Запрос `ping` возвращает текущий баланс по агентскому договору с Visa QIWI Wallet.

## Формат запроса

~~~xml
<request>
<request-type>ping</request-type>
<terminal-id>44</terminal-id>
<extra name="password">password</extra>
</request>
~~~

### Параметры запроса

Параметр|Описание
-|-
*request*| Группирующий тег. Дочерние теги содержат параметры платежа.
*request-type* | Тип запроса (идентификатор запроса баланса договора Visa QIWI Wallet: *ping*)
*terminal-id* | Идентификатор агента в системе Visa QIWI Wallet
*extra name="password"* | Экстра-поле, содержащее пароль для аутентификации в системе Visa QIWI Wallet

## Формат ответа

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

Параметры ответа:

Параметр|Описание|Атрибуты
-|-|-
*response*	| Группирующий тег ответа.|Отсутствуют.
*result-code* | [Код результата](#tech_error) выполнения запроса.| `fatal` - логический признак фатальности ошибки обработки платежа.
*balances*|Группирующий тег, содержит информацию о балансе всех активных счетов Контрагента в системе Visa QIWI Wallet.|Отсутствуют.
*balance* | Текущий баланс единичного счета Контрагента в системе Visa QIWI Wallet. | `code` - цифровой код валюты счета (в формате ISO 4217).
