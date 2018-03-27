# Модели

Модель представляет набор структурированных данных, называемых записями (records).  Модели обычно соответствуют таблицам/коллекциям в БД, атрибуты соответствуют столбцам/полям, а записи (records) соответствуют строкам/документам.

### Объявление моделей

По соглашению, модели объявляются созданием файлов в папке `api/models/` приложения Sails :

```javascript
// api/models/Product.js
module.exports = {
  attributes: {
    nameOnMenu: { type: 'string', required: true },
    price: { type: 'string', required: true },
    percentRealMeat: { type: 'number', defaultsTo: 20, columnType: 'FLOAT' },
    numCalories: { type: 'number' },
  },
};
```

Для полного ознакомления с доступными параметрами при настройке определения модели, см. [Model Settings](http://sailsjs.com/documentation/concepts/models-and-orm/model-settings), [Attributes](http://sailsjs.com/documentation/concepts/models-and-orm/attributes), and [Associations](http://sailsjs.com/documentation/concepts/models-and-orm/associations).

<!--
commented-out content at: https://gist.github.com/rachaelshaw/1d7a989f6685f11134de3a5c47b2ebb8#1


commented-out content at: https://gist.github.com/rachaelshaw/1d7a989f6685f11134de3a5c47b2ebb8#2
-->



### Использование модели

Когда Sails приложение запускается, его модели становятся доступны из его controller actions, helpers, tests, и just about anywhere else you normally write code.  Это позволяет вашему коду вызывать методы модели для взаимодействия с вашей БД (или даже с несколькими БД).

На моделях доступно множество встроенных методов, наиболее важными из которых являются методы запросов, такие как [.find()](http://sailsjs.com/documentation/reference/waterline/models/find) и [.create()](http://sailsjs.com/documentation/reference/waterline/models/create).  Вы можете найти подробную документацию по использованию таких методов в [Reference > Waterline (ORM) > Models](http://sailsjs.com/documentation/reference/waterline-orm/models).


### Методы запросов

Каждая модель в Waterline удет иметь набор методов запросов, открытых на нем, чтобы вы могли взаимодействовать с базой данных нормальным образом. Что известно как CRUD (Create-Read-Update-Delete) методы и это основной способ управления вашими данными.

Поскольку они должны отправлять запрос в базу данных и ждать ответа, методы запросов являются **асинхронными функциями**.  То есть, они не возвращаются с ответом сразу.  Как и другие асинхронные функции в JavaScript (`setTimeout()` например), Это означает, что нам нужен другой способ определения, когда они закончили выполнение, были ли они успешными, и если нет какая ошибка (или других исключительных обстоятельств) произошла.

В Node.js, Sails, и JavaScript обычно, классическим способом поддержки этой парадигмы является использование _callbacks_.

##### Callbacks

По соглашению, встроенные в модели методы возвращают _отсроченный(deferred) объект_ называемый "query":

```javascript
var query = User.findOne({ name: 'Rose' });
```

После запуска [кода выше](https://gist.github.com/mikermcneil/c6a033d56497e9930a363a2949284fd3), наше приложение _на самом деле_ еще не общается с БД.  Чтобы действительно выполнить запрос, нужно вызвать `.exec(cb)` на этом deferred object, где `cb` это коллбэк-функция запускаемая после успешного завершения запроса:

```javascript
query.exec(function (err, rose) {
  if (err) { return res.serverError(err); }
  if (!rose) { return res.notFound(); }
  return res.json(rose);
});
```

> В дополнение к `.exec()`, многим приложениям Sails выгодно использовать библиотеку [async](https://www.npmjs.com/package/async).  Фактически, для облегчения этого, Sails предоставляет [простой способ](http://sailsjs.com/documentation/reference/configuration/sails-config-globals) доступа к `async` во всем приложении.


##### Promises

Как альтернатива коллбэкам, Waterline также включает опциональную поддержку promises.  Вместо вызова `.exec()` на запросе, вы можете выбрать вызов `.then()`, `.spread()`, или `.catch()`, который начнет выполнение запроса и вернет [Bluebird promise](https://github.com/petkaantonov/bluebird).

> Если вы еще не являетесь активным пользователем promises, не волнуйтесь-- [just stick with `.exec()`](https://github.com/balderdashy/sails/issues/3459#issuecomment-171039631).  Решение о том, следует ли использовать обратные вызовы или обещания, является вопросом стиля, поэтому не парьтесь что правильнее.

### Resourceful pubsub методы

Sails также предоставляет несколько других "resourceful pubsub" (или "RPS") методов, специально предназначенные для выполнения простых операций в реальном времени с использованием динамических комнат.  Для получения дополнительной информации об этих методах см.
 [Reference > WebSockets > Resourceful PubSub](http://sailsjs.com/documentation/reference/web-sockets/resourceful-pub-sub).


### Кастомные методы моделей

Помимо встроеной функциональности Sails, вы можете определять собственные методы моделей.  Позьзовательские методы модели наиболее полезны для экстраполяции кода контроллера, который относится к конкретной модели; т.e. это позволяет извлечь код из ваших контроллеров и повторно использовать функции, которые можно вызывать из любого места (т.e. не зависимо от `req` or `res`.)

> Эта особенность использует тот факт, что модели игнорируют неопознанные параметры, поэтому вам нужно быть осторожными чтобы случайно не переопределить встроенные методы (не создавайте методы с именем "create", и т.п.)

Методы моделей могут быть и синхронными и асинхронными функциями, но чаще всего, они _asynchronous_.  По соглашению, асинхронные методы моделей должны быть 2-ary functions, которые принимают `options` как свой первый аргумент, и коллбэк в Node-стиле как второй аргумент.  Вместо коллбэка можно возвращать promise (обе стратегии работают просто отлично - это вопрос предпочтения.  Если у вас нет preference, придерживайтесь Node callbacks.)

##### Хорошие практики

Одна из лучших практик заключается в том, чтобы написать свой метод статической модели, чтобы он мог принимать либо запись OR, либо ее значение первичного ключа.  Для моделей, которые работают/from _multiple_ records at once, вы должны разрешить массив записей OR массив значений первичного ключа, который должен быть передан.  Это занимает больше времени для написания, но делает ваш метод намного более мощным. И так как вы делаете это, чтобы экстраполировать часто используемую логику, это обычно стоит дополнительных усилий.

Например:

```js
// в api/models/Monkey.js...

// Ищем monkeys с тем же именем, что и указанное лицо
findWithSameNameAsPerson: function (opts, cb) {

  var person = opts.person;

  // Прежде чем делать что-либо еще, проверьте, имеет ли значение первичного ключа
  // был принят вместо записи, и если да, то поиск, который
  // человек, о котором мы даже говорим:
  (function _lookupPersonIfNecessary(afterLookup){
    // (это само-вызываемая функция просто для краткости)
    if (typeof person === 'object')) return afterLookup(null, person);
    Person.findOne(person).exec(afterLookup);
  })(function (err, person){
    if (err) return cb(err);
    if (!person) {
      err = new Error();
      err.message = require('util').format('Невозможно найти monkeys с тем же именем, что и персона w/ id=%s потому что такая персона не существует.', person);
      err.status = 404;
      return cb(err);
    }

    Monkey.find({ name: person.name })
    .exec(function (err, monkeys){
      if (err) return cb(err);
      cb(null, monkeys);
    })
  });

}
```

Затем вы можете:

```js
Monkey.findWithSameNameAsPerson(albus, function (err, monkeys) { ... });
// -or-
Monkey.findWithSameNameAsPerson(37, function (err, monkeys) { ... });
```

> For more tips, read about the incident involving [Timothy the Monkey]().

##### Что насчет instance methods?

Начиная с версии Sails v1.0, instance methods были убраны из Sails и Waterline.  While instance methods like `.save()` and `.destroy()` were sometimes convenient in app code, in Node.js at least, многие пользователи обнаружили, что они привели к непредвиденным последствиям и ловушкам проектирования.

НАпример, рассмотрите приложение, которое управляет свадебными записями.  Может показаться хорошей идеей записывать как instance method в модели Person для обновления атрибута `spouse` (супруг) в обоих индивидуумах в БД.  Это позволит вам написать код контроллера, например:

```js
personA.marry(personB, function (err) {
  if (err) { return res.serverError(err); }
  return res.ok();
})
```

Выглядит здорово... пока не придет время для осуществления немного другого действия с примерно той же логикой, где доступны только данные об id of "personA" (а не вся запись.)  ТОгда, вам придется переписывать ваш instance method как static method всеравно!

Лучше всего писать custom (static) метод модели с самого начала. Это делает вашу функцию более reusable/универсальной, поскольку он будет доступен независимо от того, имеется ли у вас фактический экземпляр записи.  Вы можете реорганизовать код из предыдущего примера, чтобы выглядеть так:

```js
Person.marry(personA.id, personB.id, function (err) {
  if (err) { return res.serverError(err); }
  return res.ok();
})
```

### Чувствительность к регистру

Запросы в Sails 1.0 are no longer forced to be case *insensitive* regardless of how the database processes the query. This leads to much improved query performance and better index utilization. Most databases are case *sensitive* by default but in the rare cases where they aren't and you would like to change that behavior you must modify the database to do so.

For example by default MySQL will use a database collation that is case *insensitive* which is different from sails-disk so you may experience different results from development to production. In order to fix this you can set the tables in your MySQL database to a case *sensitive* collation such as `utf8_bin`.


<!--
commented-out content at: https://gist.github.com/rachaelshaw/1d7a989f6685f11134de3a5c47b2ebb8#3


commented-out content at: https://gist.github.com/rachaelshaw/1d7a989f6685f11134de3a5c47b2ebb8#4

commented-out content at: https://gist.github.com/rachaelshaw/1d7a989f6685f11134de3a5c47b2ebb8#5

commented-out content at: https://gist.github.com/rachaelshaw/1d7a989f6685f11134de3a5c47b2ebb8#6
-->

<docmeta name="displayName" value="Models">
