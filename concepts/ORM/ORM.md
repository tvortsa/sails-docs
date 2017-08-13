# Waterline: SQL/noSQL Data Mapper (ORM/ODM)


Sails поставляется с мощной [ORM/ODM](http://stackoverflow.com/questions/12261866/what-is-the-difference-between-an-orm-and-an-odm) called [Waterline](https://github.com/balderdashy/waterline), нструмент агрегирования хранилища данных который радикально упрощает взаимодействие с одной и более БД [databases](http://www.cs.umb.edu/cs630/hd1.pdf).  Он представлен как слой абстракции поверх подлежащей БД, allowing you to easily query and manipulate your data _without_ writing vendor-specific integration code.

### Database Agnosticism

В основанных на схемах БД [Postgres](http://www.postgresql.org/), [Oracle](https://www.oracle.com/database), и [MySQL](http://www.mysql.com), модели представлены таблицами.  В [MongoDB](http://www.mongodb.org), они представлены Mongo "коллекциями".  В [Redis](http://redis.io), они предсталяются используя пары ключ/значение. Each database has its own distinct query dialect, and in some cases even requires installing and compiling a specific native module to connect to the server.  This involves a fair amount of overhead, and garners an unsettling level of [vendor lock-in](https://en.wikipedia.org/wiki/Vendor_lock-in) to a specific database; e.g. if your app uses a bunch of SQL queries, it will be very hard to switch to Mongo later, or Redis, and vice versa.

Waterline query syntax floats above all that, focusing on business logic like creating new records, fetching/searching existing records, updating records, or destroying records.  No matter what database you're contacting, the usage is _exactly the same_.  Furthermore, Waterline allows you to [`.populate()`](http://sailsjs.com/documentation/reference/waterline/queries/populate.html) associations between models, _even if_ the data for each model lives in a different database.  That means you can switch your app's models from Mongo, to Postgres, to MySQL, to Redis, and back again - without changing any code.  For the times when you need low-level, database-specific functionality, Waterline provides a query interface that allows you to talk directly to your models' underlying database driver (see [.query()](http://sailsjs.com/documentation/reference/waterline/models/query.html) and [.native()](http://sailsjs.com/documentation/reference/waterline/models/native.html).)



### Сценарий

Представим что строим e-commerce website, с мобильным приложением впридачу.  Users browse products by category or search for products by keyword, then they buy them.  That's it!  Some parts of your app are quite ordinary; you have an API-driven flow for logging in, signing up, order/payment processing, resetting passwords, etc. However, you know there are a few mundane features lurking in your roadmap that will likely become more involved.  Sure enough:

##### Гибкость

_You ask the business what database they would like to use:_

> "Datab... what?  Let's not be hasty, wouldn't want to make the wrong choice.  I'll get ops/IT on it.  Go ahead and get started though."

The traditional methodology of choosing one single database for a web application/API is actually prohibitive for many production use cases.  Oftentimes the application needs to maintain compatibility with one or more existing data sets, or it is necessary to use a few different types of databases for performance reasons.

Since Sails uses `sails-disk` by default, you can start building your app with zero configuration, using a local temporary file as storage.  When you're ready to switch to the real thing (and when everyone knows what that even is), just change your app's [datastore configuration](http://sailsjs.com/documentation/reference/configuration/sails-config-datastores).



##### Совместимость

_The product owner/stakeholder walks up to you and says:_

> "Oh hey by the way, the products actually already live in our point of sale system. It's some ERP thing I guess, something like "DB2"?  Anyways, I'm sure you'll figure it out- sounds easy right?"

Many enterprise applications must integrate with an existing database.  If you're lucky, a one-time data migration may be all that's necessary, but more commonly, the existing dataset is still being modified by other applications.  In order to build your app, you might need to marry data from multiple legacy systems, or with a separate dataset stored elsewhere.  These datasets could live on 5 different servers scattered across the world! One colocated database server might house a SQL database with relational data, while another cloud server might hold a handful of Mongo or Redis collections.

Sails/Waterline lets you hook up different models to different datastores; locally or anywhere on the internet.  You can build a User model that maps to a custom MySQL table in a legacy database (with weird crazy column names).  Same thing for a Product model that maps to a table in DB2, or an Order model that maps to a MongoDB collection.  Best of all, you can `.populate()` across these different datastores and adapters, so if you configure a model to live in a different database, your controller/model code doesn't need to change (note that you _will_ need to migrate any important production data manually)

##### Производительность

_Вы сидите перед своим ноутбуком поздно ночью, и понимаете:_
> "Как я могу выполнить поиск по ключевым словам?  В данных продукта нет ключевых слов, И бизнес хочет, чтобы результаты поиска оценивались на основе n-грамматических последовательностей слов.  Также я понятия не имею, как этот механизм рекомендации будет работать.  Также, если я слышу слова `big data` еще раз сегодня вечером я ухожу и возвращаюсь к работе в кафе."

So what about the "big data"?  Normally when you hear bloggers and analyst use that buzzword, you think of data mining and business intelligence.  You might imagine a process that pulls data from multiple sources, processes/indexes/analyzes it, then writes that extracted information somewhere else and either keeps the original data or throws it away.

However, there are some much more common challenges that lend themselves to the same sort of indexing/analysis needs; features like "driving-direction-closeness" search, or a recommendation engine for related products.  Fortunately, a number of databases simplify specific big-data use cases (for instance MongoDB provides geospatial indexing, and ElasticSearch provides excellent support for indexing data for full-text search).

Using databases in the way they're intended affords tremendous performance benefits, particularly when it comes to complex report queries, searching (which is really just customized sorting), and NLP/machine learning.  Certain databases are very good at answering traditional relational business queries, while others are better suited for map/reduce-style processing of data, with optimizations/trade-offs for blazing-fast read/writes.  This consideration is especially important as your app's user-base scales.

### Adapters

Like most MVC frameworks, Sails supports [multiple databases](http://sailsjs.com/features).  That means the syntax to query and manipulate our data is always the same, whether we're using MongoDB, MySQL, or any other supported database.

Waterline builds on this flexibility with its concept of adapters.  An adapter is a bit of code that maps methods like `find()` and `create()` to a lower-level syntax like `SELECT * FROM` and `INSERT INTO`.  The Sails core team maintains open-source adapters for a handful of the [most popular databases](http://sailsjs.com/features), and a wealth of [community adapters](https://github.com/balderdashy/sails-docs/blob/0.9/Database-Support.md) are also available.

Custom Waterline adapters are actually [pretty simple to build](https://github.com/balderdashy/sails-generate-adapter), and can make for more maintainable integrations; anything from a proprietary enterprise account system, to a cache, to a traditional database.


### Datastores

 **datastore** представляет собой конкретную конфигурацию базы данных.  This configuration object includes an adapter to use, as well as information like the host, port, username, password, and so forth.  Datastores are defined in the Sails config [`config/datastores.js`](http://sailsjs.com/documentation/reference/configuration/sails-config-datastores).

```javascript
// in config/datastores.js
// ...
{
  adapter: 'sails-mysql',
  host: 'localhost',
  port: 3306,
  user: 'root',
  password: 'g3tInCr4zee&stUfF'
}
// ...
```


### Analogy

Imagine a file cabinet full of completed pen-and-ink forms. All of the forms have the same fields (e.g. "name", "birthdate", "maritalStatus"), but for each form, the _values_ written in the fields vary.  For example, one form might contain "Lara", "2000-03-16T21:16:15.127Z", "single", while another form contains "Larry", "1974-01-16T21:16:15.127Z", "married".

Now imagine you're running a hotdog business.  If you were _very_ organized, you might set up your file cabinets as follows:

+ **Employee** (contains your employee records)
  + `fullName`
  + `hourlyWage`
  + `phoneNumber`
+ **Location** (contains a record for each location you operate)
  + `streetAddress`
  + `city`
  + `state`
  + `zipcode`
  + `purchases`
    + a list of all the purchases made at this location
  + `manager`
    + the employee who manages this location
+ **Purchase** (contains a record for each purchase made by one of your customers)
  + `madeAtLocation`
  + `productsPurchased`
  + `createdAt`
+ **Product** (contains a record for each of your various product offerings)
  + `nameOnMenu`
  + `price`
  + `numCalories`
  + `percentRealMeat`
  + `availableAt`
    + a list of the locations where this product offering is available.


В Sails приложении, **model** это кабинет.  Он содержит **records**, которые представляют формы.  `Attributes` как поля в каждой форме.



### Notes
+ This documentation on models is not applicable if you are overriding the built-in ORM, [Waterline](https://github.com/balderdashy/waterline).  In that case, your models will follow whatever convention you set up, on top of whatever ORM library you're using (e.g. Mongoose.)





<docmeta name="displayName" value="Models and ORM">
