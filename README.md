![](/api.jpg?raw=true)
## Начало работы
Для начала использования, вам необходимо получить токен.Токен находится в [приложении](https://vk.com/app7531387).Если у вас есть доступ к API,то в конце списка будет кнопка "API".После генерации токена сохраните его к себе, так как потом вы не сможете его узнать из приложения,и придётся заново сгенерировать.

---

Все запросы должны посылаться методом **POST**, Content-Type application/json:

```
https://emereld-coin.tk:2096/<method>
```  



# API
#### **sendPayment**
Делает перевод другому пользователю

```js
const axios = require('axios');

axios.post('http://emereld-coin.tk:2096/sendPayment', {
    merchantId: 112653548,
    toId: 212653548,
    amount: 1,
    token: "7{H6rY6DPf~W#mb6mtI3JY4W9yB8j@?d"
})
  .then(function (response) {
    console.log(response);
})
  .catch(function (error) {
    console.log(error);
});
```

|Параметр|Тип|Описание|
|-|-|-|
|merchantId|Number|Айди отправителя|
|toId|Number|Айди получателя|
|amount|Number|Сумма перевода(в целых числах,больше или равно 1)|
|token|String|Токен отправителя|

Пример ответа в случае **успеха**:
```yaml
{response: "ok"}
```
Пример ответа в случае **ошибки**:
```yaml
{response: "error",description:"User does not exist"}
```
#
#### **getLink**
Получает ссылку для перевода

```js
const axios = require('axios');

axios.post('http://emereld-coin.tk:2096/getLink', {
    merchantId: 112349092
})
  .then(function (response) {
    console.log(response);
})
  .catch(function (error) {
    console.log(error);
});
```

|Параметр|Тип|Описание|
|-|-|-|
|merchantId|Number|ID получателя перевода|

Пример ответа в случае **успеха**:
```yaml
{response: "ok", link:"https://vk.com/app7531387#transfer_112349092_1"}
```
Пример ответа в случае **ошибки**:
```yaml
{response: "error", description:"User does not exist"}
```
#
#### **getBalance**
Получает баланс пользователя

```js
const axios = require('axios');

axios.post('http://emereld-coin.tk:2096/getBalance', {
    userId: 112653548,
    token: "7{H6rY6DPf~W#mb6mtI3JY4W9yB8j@?d",
    merchantId: 212633542
})
  .then(function (response) {
    console.log(response);
})
  .catch(function (error) {
    console.log(error);
});
```

|Параметр|Тип|Описание|
|-|-|-|
|userId|Number|ID пользователя,у которого хотите узнать баланс|
|token|String|Токен|
|merchantId|Number|Айди владельца токена|

Пример ответа в случае **успеха**:
```yaml
{response: "ok", userId:112653548, balance:10.1}
```
Пример ответа в случае **ошибки**:
```yaml
{response: "error", description:"Invalid token"}
```

---

# Callback API

Чтобы получить уведомление о переводе - необходимо подключить свой callback сервер.
```js
const axios = require('axios');

axios.post('http://emereld-coin.tk:2096/setCallback', {
    merchantId: 112653548,
    callback: "https://emeraldcoin-games.ru/callback"
    token: "7{H6rY6DPf~W#mb6mtI3JY4W9yB8j@?d"
})
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```

|Параметр|Тип|Описание|
|-|-|-|
|merchantId|Number|Айди владельца сервера и токена|
|callback|String|Адрес callback сервера|
|token|String|Токен|

Ваш сервер должен ответить строкой: ```emeraldapi```

Если необходмо отвязать сервер,то передайте в параметре callback строку **```del```**

При переводе EC, на ваш сервер будет передан POST запрос с JSON следующей структуры:

```yaml
{ 
  from_id: 497273766,
  to_id: 212653548,
  created_at: 1612101622,
  sum:12,
  hash: 'ad22744f6faa99dd8305430eb42c9728d5b539c39e3954d761bbbe97d681ac45' 
}
```
|Параметр|Тип|Описание|
|-|-|-|
|from_id|Number|Айди отправителя|
|to_id|Number|Айди получателя|
|created_at|Number|Время перевода|
|sum|Number|Сумма перевода|
|hash|String|Хэш строка подлинности перевода|

Хэш формируется следующим образом:
```sha256(from_id;to_id;created_at;sum;token)```
token - ваш токен

Ваш сервер должен ответить текстом ok. В противном случае будет сделана вторая попытка отправить запрос через ~7-8 сек.Если ваш сервер перестанет отвечать долго,то запросы начнут копиться.После того,как ваш сервер начнёт функционировать,то все неудавшиеся запросы будут автоматически присланы вам.

#
#### **errorTimes**
Возвращает количество неудачных запросов на ваш сервер

```js
const axios = require('axios');

axios.post('http://emereld-coin.tk:2096/errorTimes', {
    token: "7{H6rY6DPf~W#mb6mtI3JY4W9yB8j@?d",
    merchantId: 212633542
})
  .then(function (response) {
    console.log(response);
})
  .catch(function (error) {
    console.log(error);
});
```

|Параметр|Тип|Описание|
|-|-|-|
|token|String|Токен|
|merchantId|Number|Айди владельца токена|

Пример ответа в случае **успеха**:
```yaml
{response: "ok", errorTimes: 3}
```
Пример ответа в случае **ошибки**:
```yaml
{response: "error", description:"Invalid token"}
```
