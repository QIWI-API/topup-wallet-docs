# Выплаты через СБП {#sbp_payment}

###### [Редактировать на GitHub](https://github.com/QIWI-API/topup-wallet-doc/blob/master/_topup_sbp_ru.html.md)

Операция используется для выплаты средств через Систему быстрых платежей (СБП). Позволяет совершать выплаты в любой банк-участник СБП по номеру мобильного телефона.

После выполнения запроса платеж начинает жизненный цикл в системе QIWI Wallet. Каждому этапу жизненного цикла соответствует определённый [статус платежа](#statuses). Цикл заканчивается после получения финального статуса платежа. Признак финального статуса указан в списке возвращаемых API [статусов платежа](#statuses).

Если в [ответе на запрос выплаты](#sbp-payment-res) получен нефинальный статус платежа, то для проверки успешного прохождения платежа вы должны периодически (но не чаще одного раза в 10 минут) выполнять [запрос проверки статуса платежа](#status) до получения успешного или неуспешного **финального** статуса платежа.

## Формат запроса {#sbp-payment-req}

[Справка по формату значений](#params-types) тегов и атрибутов XML.

### Параметры запроса

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<request>
  <request-type>pay</request-type>
  <terminal-id>123</terminal-id>
  <extra name="password">***</extra>
  <auth>
    <payment>
      <transaction-number>12345678</transaction-number>
      <from>
        <ccy>RUB</ccy>
      </from>
      <to>
        <amount>10007.03</amount>
        <ccy>RUB</ccy>
        <service-id>38413</service-id>
        <account-number>70070310009</account-number>
        <extra name="bankId">100000000008</extra>
      </to>
    </payment>
  </auth>
</request>
~~~

Тег|Описание
--------|------
*request*|Группирующий тег. Дочерние теги содержат параметры платежа.
*request-type* | Тип запроса (идентификатор запроса выплаты: `pay`)
*terminal-id* | Идентификатор агента в системе QIWI Wallet
*extra name="password"* | Экстра-поле, содержащее пароль для аутентификации в системе QIWI Wallet
*auth*|Группирующий тег, описывает платежные данные
*payment*|Группирующий тег, описывает единичный платеж. **В запросе может присутствовать только один тег `payment`.**
*transaction-number* | Номер транзакции платежа в информационной системе агента. Его необходимо использовать при [проверке статуса платежа](#status). Номер транзакции и идентификатор `terminal-id` однозначно определяют платёж в системе QIWI Wallet.
*from* | Группирующий тег, содержит информацию о сумме, полученной агентом от клиента
*from/ccy* | Валюта счета агента, с которого будет произведено списание денежных средств. Указывается цифровой или буквенный код валюты по ISO 4217.
*from/service-id* | Идентификатор источника (канала) пополнения (необязательный параметр). Параметр используется в случае необходимости разделения потоков пополнения, сохраняя единый баланс агента.
*to*|Группирующий тег, содержит информацию о платеже
*to/amount* | Сумма к зачислению
*to/ccy* | Валюта выплаты (только `RUB`)
*to/service-id* | Идентификатор сервиса, на который производится зачисление средств при выплате (константа: `38413`)
*to/account-number* | Идентификатор Получателя в СБП
*to/extra name="bankId"* | Идентификатор Банка Получателя в СБП. Список банков, доступных для оплаты через СБП: в формате xlsx ([скачать](/downloads/sbp_members_b2c_receiver.xlsx)), csv ([скачать](/downloads/sbp_members_b2c_receiver.csv)).

## Формат ответа {#sbp-payment-res}

При возникновении сетевых ошибок (например, таймауты при соединении или чтении ответа), HTTP-ошибок (HTTP-статус не равен 200, пустой ответ), некорректных XML-документов (например, c отсутствующими обязательными тегами и/или атрибутами) вы должны перейти к периодическому [запросу статуса](#status) до получения успешного или неуспешного финального статуса платежа. Поскольку при возникновении данных ошибок информация о статусе платежа не доступна, вы не должны отклонять платёж на своей стороне.

Формат ответа API зависит от того, как сервер обработал запрос:

* [ответ без ошибок обработки запроса](#sbp_res_payment) — в случае, когда запрос обработан корректно;
* [ответ с ошибкой обработки запроса](#sbp_res_tech) — в случае, когда сервер не смог обработать запрос (информация о платеже не получена).

<aside class="success">Если вы не уверены в корректной расшифровке ответа API, обратитесь в Службу поддержки: <a href="mailto:bss@qiwi.com">bss@qiwi.com</a>.</aside>

### Ответ без ошибок обработки {#sbp_res_payment}

~~~xml
<response>
  <result-code fatal="false">0</result-code>
  <payments>
    <payment status='50' txn_id='20323739405' transaction-number='12345678' result-code='0' message='Ок' final-status='false' fatal-error='false' txn-date='14.04.2022 09:40:09'  >
      <from>
        <amount>10007.03</amount>
        <ccy>643</ccy>
      </from>
      <to>
        <service-id>38413</service-id>
        <amount>10007.03</amount>
        <ccy>643</ccy>
        <account-number>70070310009</account-number>
      </to>
    </payment>
  </payments>
  <balances><balance code="643">6776332.87</balance></balances><balances-ovd><balance-ovd code="643">6776332.87</balance-ovd></balances-ovd><f></f><lk-status>001</lk-status></response>
~~~

Если запрос обработан корректно, то в ответе возвращаются сведения о платеже в теге `<payment>`.

Параметры ответа:

Тег| Описание                                                                                                |Атрибуты
--------|---------------------------------------------------------------------------------------------------------|---------
*response*| Группирующий тег ответа.                                                                                |Отсутствуют.
*result-code* | [Код ошибки обработки запроса](#tech_error).                                                        | `fatal` – логический признак фатальности ошибки обработки запроса в целом.
*payment* | Описание принятого платежа.                                                                             | `status` – [статус платежа](#statuses) в системе QIWI Wallet;<br>`txn_id` – идентификатор транзакции платежа в системе QIWI Wallet. **Если тег не заполнен, платеж не был зарегистрирован из-за временной ошибки. Попробуйте повторить запрос позже**;<br>`transaction-number` – номер транзакции платежа в информационной системе Контрагента;<br>`result-code` – [код ошибки обработки платежа](#error);<br>`message`, `msg` - текстовое описание ошибки;<br>`final-status` – логический признак финального статуса платежа;<br>`fatal-error` - логический признак фатальности ошибки обработки платежа (фатальная ошибка означает, что повторный запрос с теми же реквизитами приведет к повторению той же ошибки);<br>`txn-date` – дата приема платежа в систему QIWI Wallet.
*from*| Группирующий тег, содержит информацию о списанных средствах.                                            |Отсутствуют.
*from/amount* | Сумма, списанная со счета Контрагента.                                                                  |Отсутствуют.
*from/ccy* | Валюта счета (в качестве значения используется цифровой или буквенный код валюты по ISO 4217).          |Отсутствуют.
*to*| Группирующий тег, содержит информацию о платеже.                                                        |Отсутствуют.
*to/service-id* | Идентификатор сервиса, на который производится зачисление средств при выплате.                          |Отсутствуют.
*to/amount* | Сумма к зачислению.                                                                                     |Отсутствуют.
*to/ccy* | Валюта выплаты (цифровой или буквенный код валюты по ISO 4217).                                         |Отсутствуют.
*to/account-number* | Идентификатор Получателя.                                                                    |Отсутствуют.
*balances* | Группирующий тег, содержит информацию о балансе всех активных счетов Контрагента в системе QIWI Wallet. |Отсутствуют.
*balances/balance* | Текущий баланс единичного счета Контрагента в системе QIWI Wallet                                       | `code` - цифровой код валюты счета (в формате ISO 4217).

### Ответ с ошибками обработки {#sbp_res_tech}

Если сервер не смог обработать запрос, API возвращает ответ с кодом произошедшей ошибки. **В этом случае информация о платеже отсутствует в ответе, поэтому вы должны перейти к периодическому [запросу статуса](#status), не отклоняя платеж на своей стороне**.

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<response>
  <result-code fatal="false" message="Неизвестная ошибка" msg="Неизвестная ошибка">300</result-code>
</response>
~~~

Параметры ответа:

Тег| Описание                                         |Атрибуты
--------|--------------------------------------------------|---------
*result-code* | [Код ошибки обработки запроса](#tech_error) | `fatal` – логический признак фатальности ошибки обработки запроса. Фатальность ошибки означает, что повторный запрос с теми же параметрами приведет к повторению той же ошибки. Фатальные ошибки обработки запроса, как правило, вызваны ошибками конфигурирования и требуют ручного вмешательства (обращения в службу поддержки сервиса QIWI Wallet по адресу  <a href="mailto:bss@qiwi.com">bss@qiwi.com</a>). При появлении таких ошибок вы можете либо не приостанавливать выполнение повторных запросов, либо приостановить до устранения ошибок конфигурирования;<br>`message`, `msg` - текстовое описание ошибки.
