# API Emeralad Coin

## Начало работы
Для начала использования, вам необходимо получить токен.Токен находится в [приложении](https://vk.com/app7531387).Если у вас есть доступ к API,то в конце списка будет кнопка "API".После генерации токена сохраните его к себе, так как потом вы не сможете его узнать из приложения,и придётся заново сгенерировать.

---

Все запросы должны посылаться методом **POST**, Content-Type application/json:

```
http://emereld-coin.tk:9083/<method>
```  



# API
#### **sendPayment**
Делает перевод другому пользователю

```js
const axios = require('axios');

axios.post('http://emereld-coin.tk:9083/sendPayment', {
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
{response: "error",description:"Пользователя не существует"}
```
#
#### **getLink**
Получает ссылку для перевода

```js
const axios = require('axios');

axios.post('http://emereld-coin.tk:9083/getLink', {
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
{response: "error", description:"Пользователя не существует"}
```
#
#### **getBalance**
Получает баланс пользователя

```js
const axios = require('axios');

axios.post('http://emereld-coin.tk:9083/getBalance', {
    userId: 112653548
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
Пример ответа в случае **успеха**:
```yaml
{response: "ok", userId:112653548, balance:10.1}
```
Пример ответа в случае **ошибки**:
```yaml
{response: "error", description:"Пользователя не существует"}
```

---

# Callback API

Чтобы получить уведомление о переводе - необходимо подключить свой callback сервер.
```js
const axios = require('axios');

axios.post('http://emereld-coin.tk:9083/setCallback', {
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

При переводе EC, на ваш сервер будет передан JSON следующей структуры:

```yaml
{ 
  from_id: 2627951,
  to_id: 112653548,
  created_at: 1555612247,
  hash: '5bb8fcefd43242773e34eb485f377463' 
}
```
|Параметр|Тип|Описание|
|-|-|-|
|from_id|Number|Айди отправителя|
|to_id|Number|Айди получателя|
|created_at|Number|Время перевода|
|hash|String|Хэш строка подлинности перевода|

Хэш формируется следующим образом:
```md5(from_id;to_id;created_at;token)```
token - ваш токен

Ваш сервер должен ответить текстом ok. В противном случае будет сделана вторая попытка отправить запрос через ~10 сек.


