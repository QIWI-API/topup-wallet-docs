# Запрос баланса контрагента {#get-balance}

###### [Редактировать на GitHub](https://github.com/QIWI-API/topup-wallet-doc/blob/master/_get-balance_ru.html.md)

Данный запрос возвращает текущий баланс по агентскому договору в сервисе QIWI Кошелек.

## Формат запроса {#balance-req}

### Параметры запроса

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<request>
<request-type>ping</request-type>
<terminal-id>44</terminal-id>
<extra name="password">password</extra>
</request>
~~~

Параметр|Описание
-|-
*request*| Группирующий тег
*request-type* | Тип запроса (идентификатор запроса баланса: `ping`)
*terminal-id* | Идентификатор агента в системе QIWI Wallet
*extra name="password"* | Экстра-поле, содержащее пароль для аутентификации в системе QIWI Wallet

## Формат ответа {#balance-res}

### Ответ без ошибок обработки запроса

Если запрос обработан корректно, то в ответе возвращаются сведения об агентском балансе.

~~~xml
<?xml version="1.0" encoding="utf-8"?>
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
*response*| Группирующий тег ответа.|Отсутствуют.
*result-code* | [Код ошибки обработки запроса](#tech_error).| `fatal` - логический признак фатальности ошибки обработки платежа.
*balances*|Группирующий тег, содержит информацию о балансе всех активных счетов Контрагента в системе QIWI Wallet.|Отсутствуют.
*balance* | Текущий баланс единичного счета Контрагента в системе QIWI Wallet. | `code` - цифровой код валюты счета (в формате ISO 4217).

### Ответ с ошибками обработки запроса

Если сервер не смог обработать запрос, API возвращает ответ с кодом произошедшей ошибки.

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<response>
  <result-code fatal="false" message="Неизвестная ошибка" msg="Неизвестная ошибка">300</result-code>
</response>
~~~

Параметры ответа:

 Тег|Описание|Атрибуты
--------|------|---------
*result-code* | [Код ошибки обработки запроса](#tech_error)| `fatal` – логический признак фатальности ошибки обработки запроса;<br>`message`, `msg` - текстовое описание ошибки.
