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

Как альтернатива коллбэкам, Waterline также включает опциональную поддержку promises.  Вместо вызова `.exec()` на запросе, вы можете выбрать вызов `.then()`, `.spread()`, или `.catch()`, which will begin executing the query and return a [Bluebird promise](https://github.com/petkaantonov/bluebird).

> If you are not already an avid user of promises, don't worry-- [just stick with `.exec()`](https://github.com/balderdashy/sails/issues/3459#issuecomment-171039631).  The decision of whether to use callbacks or promises is a question of style, so don't feel pressured one way or the other.

### Resourceful pubsub methods

Sails also provides a few other "resourceful pubsub" (or "RPS") methods, specifically designed for performing simple realtime operations using dynamic rooms.  For more information about those methods, see [Reference > WebSockets > Resourceful PubSub](http://sailsjs.com/documentation/reference/web-sockets/resourceful-pub-sub).


### Custom model methods

In addition to the built-in functionality provided by Sails, you can also define your own custom model methods.  Custom model methods are most useful for extrapolating controller code that relates to a particular model; i.e. this allows you to pull code out of your controllers and into reusuable functions that can be called from anywhere (i.e. don't depend on `req` or `res`.)

> This feature takes advantage of the fact that models ignore unrecognized settings, so you do need to be careful about inadvertently overriding built-in methods (don't define methods named "create", etc.)

Model methods can be synchronous or asynchronous functions, but more often than not, they're _asynchronous_.  By convention, asynchronous model methods should be 2-ary functions, which accept `options` as their first argument, and a Node-style callback as the second argument.  Alternatively, instead of a callback, you might choose to return a promise (both strategies work just fine- it's a matter of preference.  If you don't have a preference, stick with Node callbacks.)

##### Best practices

One best practice is to write your static model method so that it can accept either a record OR its primary key value.  For model methods that operate on/from _multiple_ records at once, you should allow an array of records OR an array of primary key values to be passed in.  This takes more time to write, but makes your method much more powerful.  And since you're doing this to extrapolate commonly-used logic anyway, it's usually worth the extra effort.

For example:

```js
// in api/models/Monkey.js...

// Find monkeys with the same name as the specified person
findWithSameNameAsPerson: function (opts, cb) {

  var person = opts.person;

  // Before doing anything else, check if a primary key value
  // was passed in instead of a record, and if so, lookup which
  // person we're even talking about:
  (function _lookupPersonIfNecessary(afterLookup){
    // (this self-calling function is just for concise-ness)
    if (typeof person === 'object')) return afterLookup(null, person);
    Person.findOne(person).exec(afterLookup);
  })(function (err, person){
    if (err) return cb(err);
    if (!person) {
      err = new Error();
      err.message = require('util').format('Cannot find monkeys with the same name as the person w/ id=%s because that person does not exist.', person);
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

Then you can do:

```js
Monkey.findWithSameNameAsPerson(albus, function (err, monkeys) { ... });
// -or-
Monkey.findWithSameNameAsPerson(37, function (err, monkeys) { ... });
```

> For more tips, read about the incident involving [Timothy the Monkey]().

##### What about instance methods?

As of Sails v1.0, instance methods have been removed from Sails and Waterline.  While instance methods like `.save()` and `.destroy()` were sometimes convenient in app code, in Node.js at least, many users found that they led to unintended consequences and design pitfalls.

For example, consider an app that manages wedding records.  It might seem like a good idea to write an instance method on the Person model to update the `spouse` attribute on both individuals in the database.  This would allow you to write controller code like:

```js
personA.marry(personB, function (err) {
  if (err) { return res.serverError(err); }
  return res.ok();
})
```

Which looks great...until it comes time to implement a slightly different action with roughly the same logic, but where the only available data is the id of "personA" (not the entire record.)  In that case, you're stuck rewriting your instance method as a static method anyway!

A better strategy is to write a custom (static) model method from the get-go.  This makes your function more reusable/versatile, since it will be accessible whether or not you have an actual record instance on hand.  You might refactor the code from the previous example to look like:

```js
Person.marry(personA.id, personB.id, function (err) {
  if (err) { return res.serverError(err); }
  return res.ok();
})
```

### Case Sensitivity

Queries in Sails 1.0 are no longer forced to be case *insensitive* regardless of how the database processes the query. This leads to much improved query performance and better index utilization. Most databases are case *sensitive* by default but in the rare cases where they aren't and you would like to change that behavior you must modify the database to do so.

For example by default MySQL will use a database collation that is case *insensitive* which is different from sails-disk so you may experience different results from development to production. In order to fix this you can set the tables in your MySQL database to a case *sensitive* collation such as `utf8_bin`.


<!--
commented-out content at: https://gist.github.com/rachaelshaw/1d7a989f6685f11134de3a5c47b2ebb8#3


commented-out content at: https://gist.github.com/rachaelshaw/1d7a989f6685f11134de3a5c47b2ebb8#4

commented-out content at: https://gist.github.com/rachaelshaw/1d7a989f6685f11134de3a5c47b2ebb8#5

commented-out content at: https://gist.github.com/rachaelshaw/1d7a989f6685f11134de3a5c47b2ebb8#6
-->

<docmeta name="displayName" value="Models">
