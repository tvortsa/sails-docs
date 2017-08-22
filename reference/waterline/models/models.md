# Работа с Models

Этот раздел документации посвящен моделям и методам которые они предоставляют в Waterline прямо из коробки. Кроме того, дополнительные методы можно получать от hooks (как в [resourceful pubsub methods](http://sailsjs.com/documentation/reference/web-sockets/resourceful-pub-sub)) или могут быть написаны вручную в вашем приложении для обвертывания повторно используемого кастомного кода.

> + Более полное погружение в модели в Sails/Waterline, см [Concepts > Models and ORM > Models](http://sailsjs.com/documentation/concepts/models-and-orm/models).
> + Примеры того как объявлять модели можно посмотреть [здесь](https://gist.github.com/rachaelshaw/f5bf442b2171154aa6021846d1a250f8).




### Встроенные методы моделей

Обычно, методы моделей _асинхронны_, что значит что вы не можете просто вызвать их и использовать возвращаемые ими значения.  Вместо этого, вы должны использовать callbacks, или promises.
Most built-in model methods accept a callback as an optional final argument. If the callback is not supplied, a chainable Query object is returned, which has methods like `.where()` and `.exec()`. See [Working with Queries](http://sailsjs.com/documentation/reference/waterline-orm/queries) for more on that.


 Method                | Summary
 --------------------- | ------------------------------------------------------------------------
 `.create()`           | Создает запись состоящую из переданного объекта
 `.find()`             | Ищет массив записей отвечающую переданному критерию
 `.findOne()`          | Ищет одну запись отвечающую переданному критерию, или возвращает `null` если такой записи не найдено.
 `.update()`           | Обновляет записи отвечающие переданному критерию, setting the specified object of `attrName:value` pairs.
 `.destroy()`          | Удаляет записи отвечающие переданному критерию.
 `.findOrCreate()`     | Ищет одну запись отвечающую переданному критерию, или создает ее если такая запись не найдена.
 `.count()`            | Возвращает количество записей отвечающих переданному критерию.
 `.native()`/`query()` | Прямой вызов в подлежащий драйвер БД.
 `.stream()`           | Return a readable (object-mode) stream of records which match the specified criteria



<!-- ![screenshot of the api/models/ folder in a text editor](http://i.imgur.com/xdTZpKT.png) -->





### `sails.models`

If you need to disable global variables in Sails, you can still use `sails.models.<model_identity>` to access your models. 
> Not sure of your model's `identity`? Check out [Concepts > Models and ORM > Model settings](http://sailsjs.com/documentation/concepts/models-and-orm/model-settings#?identity).

<docmeta name="displayName" value="Models">
