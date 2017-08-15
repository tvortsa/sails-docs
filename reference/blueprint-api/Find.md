# Find (Blueprint)

Find это список записей которые соответствуют заданному критерию и (если возможно) subscribe на каждый из них.

```usage
GET /:model
```

Результат может быть отфильтрован, подвергнут паджинации, и отсортирован на основе конфигурации blueprint и/или параметров переданных в запрос.

Если action был вызван запросом сокета, запрашивающий сокет будет "subscribed" для всех возвращаемых записей. Если какая-либо из возвращенных записей впоследствии обновляется или удаляется, клиенту этого сокета будет отправлено сообщение об изменении. См [docs for Model.subscribe()](https://github.com/balderdashy/sails-docs/blob/master/reference/ModelMethods.md#subscriberequestrecordscontexts) .


### Параметры

 Параметр      | Тип         | Подробности
 -------------- | ------------ |:---------------------------------
 модель         | ((string))   | [identity](https://github.com/tvortsa/sails-docs/edit/1.0/concepts/models-and-orm/model-settings#?identity) of the containing model.<br/><br/>e.g. `'purchase'` (в `GET /purchase`)
 _*_              | ((string?))   | Чтобы фильтровать результаты на основе определенного атрибута, задайте параметр запроса с тем же именем что и атрибут объявленный в вашей модели. <br/> <br/> Например, если ваша модель `Purchase` имеет атрибут **amount** , мы должны послать `GET /purchase?amount=99.99` чтобы вернуть список заказов $99.99.
 _where_          | ((string?))   | Вместо фильтрации на основе определенного атрибута, вы можете вместо этого выбрать параметр `where` где WHERE часть [Waterline criteria](http://sailsjs.com/documentation/concepts/models-and-orm/query-language), _представленного как JSON строка.  Это позволяет вам воспользоваться преимуществами `contains`, `startsWith`, и другими sub-attribute criteria modifiers для придания мощи запросам `find()`. <br/> <br/> e.g. `?where={"name":{"contains":"theodore"}}`
 _limit_          | ((number?))   | Максимальное количество возвращаемых записей (полезно при пагинации). По умолчанию 30. <br/> <br/> e.g. `?limit=100`
 _skip_           | ((number?))   | The number of records to skip (useful for pagination). <br/> <br/> e.g. `?skip=30`
 _sort_           | ((string?))   | The sort order. By default, returned records are sorted by primary key value in ascending order. <br/> <br/> e.g. `?sort=lastName%20ASC`
 _select_         | ((string?))   | The attributes to include each record in the result, specified as a comma-delimited list.  By default, all attributes are selected.  Not valid for plural (&ldquo;collection&rdquo;) association attributes.<br/> <br/> e.g. `?select=name,age`.
 _omit_           | ((string?))   | The attributes to exclude from each record in the result, specified as a comma-delimited list.  Cannot be used in conjuction with `select`.    Not valid for plural (&ldquo;collection&rdquo;) association attributes.<br/> <br/> e.g. `?omit=favoriteColor,address`.
 _populate_       | ((string))    | If specified, overide the default automatic population process. Accepts a comma separated list of attributes names for which to populate record values. See [here](http://sailsjs.com/documentation/concepts/models-and-orm/records#?populated-values) for more information on how the population process fills out attributes in the returned list of records according to the model's defined associations.



### Пример

Find up to 30 of the newest purchases in our database:

```text
GET /purchase?sort=createdAt DESC&limit=30
```

[![Run in Postman](https://s3.amazonaws.com/postman-static/run-button.png)](https://www.getpostman.com/run-collection/96217d0d747e536e49a4)

##### Expected Response

e.g.
```json
[
 {
   "amount": 49.99,
   "id": 1,
   "createdAt": 1485551132315,
   "updatedAt": 1485551132315
 },
 {
   "amount": 99.99,
   "id": 47,
   "createdAt": 1485551158349,
   "updatedAt": 1485551158349
 }
]
```


##### Using jQuery

> See [jquery.com](http://jquery.com/) for more documentation.

```javascript
$.get('/purchase?sort=createdAt DESC', function (purchases) {
  console.log(purchases);
});
```


##### Using sails.io.js

> See [sails.io.js](http://sailsjs.com/documentation/reference/web-sockets/socket-client) for more documentation.

```javascript
io.socket.get('/purchase?sort=createdAt DESC', function (purchases) {
  console.log(purchases);
});
```

##### Using Angular

> See [Angular](https://angularjs.org/) for more documentation.

```javascript
$http.get('/purchase?sort=createdAt DESC')
.then(function (res) {
  var purchases = res.data;
  console.log(purchases);
});
```


##### Using cURL

> You can read more about [cURL on Wikipedia](http://en.wikipedia.org/wiki/CURL).

```bash
curl http://localhost:1337/purchase?sort=createdAt%20DESC
```


### Notes

> + The example above assumes "rest" blueprints are enabled, and that your project contains a `Purchase` model.  You can quickly achieve this by running:
>
>   ```bash
>   $ sails new foo
>   $ cd foo
>   $ sails generate model purchase
>   $ sails lift
>     # You will see a prompt about database auto-migration settings.
>     # Just choose 2 (alter) and press <ENTER>.
>   ```


<docmeta name="displayName" value="find where">
<docmeta name="pageType" value="endpoint">

