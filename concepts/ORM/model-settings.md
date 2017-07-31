# Настройки модели

В Sails, свойства верхнего уровня определения модели называются **model settings**.  Они включают все из [attribute definitions](http://sailsjs.com/documentation/concepts/models-and-orm/model-settings#?attributes), в [database settings](http://sailsjs.com/documentation/concepts/models-and-orm/model-settings#?connection) модель будет использовать, а также ряд других опций.

Большая часть этой страницы посвящена полному просмотру параметров модели, поддерживаемых Sails. Но прежде чем мы начнем, давайте рассмотрим, как применять эти параметры в приложении Sails

### Обзор

Параметры модели позволяют настраивать поведение моделей в приложении Sails.  Они могут быть указаны на основе каждой модели, устанавливая свойства верхнего уровня в [model definition](http://sailsjs.com/documentation/concepts/models-and-orm/models), или в качестве стандартных значений по умолчанию в приложении [`sails.config.models`](http://sailsjs.com/documentation/reference/configuration/sails-config-models).

##### Изменение настроек модели по умолчанию

Для изменения [default model settings](http://sailsjs.com/documentation/reference/configuration/sails-config-models) разделяемых всеми моделями вашего приложения, редактируйте файл [`config/models.js`](http://sailsjs.com/documentation/anatomy/my-app/config/models-js).

Например, если вы измените `config/models.js` так чтобы он содержал `connection: 'somePostgresqlDb'`, то, предположив, что вы определили соединение с именем `somePostgresqlDb`, вы установите PostgreSQL в качестве дефолтной. Иначе говоря все модели вашего приложения будут использовать PostgreSQL DATASTORE всегда для встроенных методов модели, таких как `.create()` или `.find()` .


##### Переопределение настроек для конкретной модели

Чтобы дополнительно настроить эти параметры для конкретной модели, вы можете указать их как свойства верхнего уровня в файле определения этой модели (e.g. `api/models/User.js`).  Это приведет к переопределению настроек модели по умолчанию с тем же именем.

Например, если вы добавите `autoUpdatedAt: false` к одному из определений вашей модели (`api/models/UploadedFile.js`), То эта модель больше не будет иметь аттрибут `updatedAt`.  Но остальные модели не изменятся; они по-прежнему будут использовать настройку по умолчанию (which is `autoUpdatedAt: true`, unless you've changed it).


##### Какой подход следует использовать?

По соглашению определения атрибутов указываются в отдельных файлах модели.  Большинство других параметров модели, таких как `schema` и` connection`, должны быть указаны в приложении, если вам не нужно переопределять их для конкретной модели; например, если ваше хранилище данных по умолчанию - PostgreSQL, но у вас есть модель `CachedBloodworkReport` которая должна быть в Redis.

Теперь, когда вы знаете, какие параметры модели в целом и как их настроить, давайте пройдём и посмотрим на каждый.

--------------------


### `migrate`

```javascript
migrate: 'safe'
```

Настройка `migrate` управляет **стратегией авто-миграции** которую Sails будет запускаться каждый раз, когда ваше приложение загружается.  Вкратце, это сообщает Sails хотите ли вы попытаться автоматически перестроить tables/collections/sets/etc. в вашей базе (базах).

##### Database migrations

In the course of developing an app, you will almost always need to make at least one or two **breaking changes** to the structure of your database.  Exactly _what_ constitutes a "breaking change" depends on the database you're using:  For example, imagine you add a new attribute to one of your model definitions.  If that model is configured to use MongoDB, then this is no big deal; you can keep developing as if nothing happened.  But if that model is configured to use MySQL, then there is an extra step: a column must be added to the corresponding table (otherwise model methods like `.create()` will stop working.)  So for a model using MySQL, adding an attribute is a breaking change to the database schema.

> Even if all of your models use MongoDB, there are still some breaking schema changes to watch out for.  For example, if you add `unique: true` to one of your attributes, a [unique index](https://docs.mongodb.com/manual/core/index-unique/) must be created in MongoDB.


In Sails, there are two different modes of operation when it comes to [database migrations](https://en.wikipedia.org/wiki/Schema_migration):

1. **Manual migrations** - The art of updating your database tables/collections/sets/etc. by hand.  For example, writing a SQL query to [add a new column](http://dev.mysql.com/doc/refman/5.7/en/alter-table.html), or sending a [Mongo command to create a unique index](https://docs.mongodb.com/manual/core/index-unique/).  If the database contains data you care about (in production, for example), you must carefully consider whether that data needs to change to fit the new schema, and, if necessary, write scripts to migrate it.  A [number of](https://www.npmjs.com/package/sails-migrations) great [open-source tools](http://knexjs.org/#Migrations-CLI) exist for managing manual migration scripts, as well as hosted products like the [database migration service on AWS](https://aws.amazon.com/blogs/aws/aws-database-migration-service/).
2. **Auto-migrations** - A convenient, built-in feature in Sails that allows you to make iterative changes to your model definitions during development, without worrying about the reprecussions.  Auto-migrations should _never_ be enabled when connecting to a database with data you care about.  Instead, use auto-migrations with fake data, or with cached data that you can easily recreate.


Whenever you need to apply breaking changes to your _production database_, you should use manual database migrations. But otherwise, when you're developing on your laptop, or running your automated tests, auto-migrations can save you tons of time.


##### How auto-migrations work

When you lift your Sails app in a development environment (e.g. running `sails lift` in a brand new Sails app), the configured auto-migration strategy will run.  If you are using `migrate: 'safe'`, then nothing extra will happen at all.  But if you are using `drop` or `alter`, Sails will load every record in your development database into memory, then drop and recreate the physical layer representation of the data (i.e. tables/collections/sets/etc.)  This allows any breaking changes you've made in your model definitions, lik removing a uniqueness constraint, to be automatically applied to your development database.  Finally, if you are using `alter`, Sails will then attempt to re-seed the freshly generated tables/collections/sets with the records it saved earlier.  


| Auto-migration strategy  | Description |
|:-------------------------|:---------------------------------------------|
|`safe`                    | never auto-migrate my database(s). I will do it myself, by hand.
|`alter`                   | auto-migrate columns/fields, but attempt to keep my existing data (experimental)
|`drop`                    | wipe/drop ALL my data and rebuild models every time I lift Sails


##### Can I use auto-migrations in production?

The `drop` and `alter` auto-migration strategies in Sails exist as a feature for your convenience during development, and when running automated tests.  **They are not designed to be used with data you care about.**  Please take care to never use `drop` or `alter` with a production dataset.  In fact, as a failsafe to help protect you from doing this inadvertently, any time you lift your app [in a production environment](http://sailsjs.com/documentation/reference/configuration/sails-config#?sailsconfigenvironment), Sails _always_ uses `migrate: 'safe'`, no matter what you have configured.

In many cases, hosting providers automatically set the `NODE_ENV` environment variable to "production" when they detect a Node.js app.  Even so, please don't rely only on that failsafe, and take the usual precautions to keep your users' data safe.  Any time you connect Sails (or any other tool or framework) to a database with pre-existing production data, **do a dry run**.  Especially the very first time.  Production data is sensitive, valuable, and in many cases irreplaceable.  Customers, users, and their lawyers are not cool with it getting flushed.

As a best practice, make sure to never lift or [deploy](http://sailsjs.com/documentation/concepts/deployment) your app with production database credentials unless you are 100% sure you are running in a production environment.  A popular approach for solving this organization-wide is simply to _never_ push up production database credentials to your source code repository in the first place, and instead relying on [environment variables](http://sailsjs.com/documentation/reference/configuration) for all sensitive credentials.  (This is an especially good idea if your app is subject to regulatory requirements, or if a large number of people have access to your code base.) 


##### Are auto-migrations slow?

If you are working with a relatively large amount of development/test data, the `alter` auto-migration strategy may take a long time to complete at startup.  If you notice that a command like `npm test`, `sails console`, or `sails lift` appears to hang, consider decreasing the size of your development dataset.  (Remember: Sails auto-migrations should only be used on your local laptop/desktop computer, and only with small, development datasets.)




### `schema`

```javascript
schema: true
```

A flag to toggle schemaless or schema mode in databases that support schemaless data structures. If turned off, this will allow you to store arbitrary data in a record. If turned on, only attributes defined in the model's `attributes` object will be stored.

For adapters that don't require a schema, such as Mongo or Redis, the default setting is `schema:false`.



### `connection`

```javascript
connection: 'my-local-postgresql'
```

The configured database [connection](http://sailsjs.com/documentation/reference/sails.config/sails.config.connections.html) where this model will fetch and save its data.  Defaults to `localDiskDb`, the default connection that uses the `sails-disk` adapter.


### `identity`

```javascript
identity: 'purchase'
```

The lowercase unique key for this model, e.g. `user`.  By default, a model's `identity` is inferred automatically by lowercasing its filename.  You should never change this property on your models.

### `globalId`

```javascript
globalId: 'Purchase'
```

This flag changes the global name by which you can access your model (if the globalization of models is enabled).  You should never change this property on your models. To disable globals, see [`sails.config.globals`](http://sailsjs.com/documentation/concepts/Globals?q=disabling-globals).



### autoPK

```javascript
autoPK: true
```

A flag to toggle the automatic definition of a primary key in your model. The details of this default PK vary between adapters (e.g. MySQL uses an auto-incrementing integer primary key, whereas MongoDB uses a randomized string UUID).  In any case, the primary keys generated by autoPK will be unique. If turned off no primary key will be created by default, and you will need to define one manually, e.g.:

```js
attributes: {
  sku: {
    type: 'string',
    primaryKey: true,
    unique: true
  }
}
```

### `autoCreatedAt`

```javascript
autoCreatedAt: true
```

If set to `false`, this disables the automatic definition of a `createdAt` attribute in your model.  By default, `createdAt` is an attribute which will be automatically set when a record is created with the current (timezone-agnostic) timestamp.   If set to a string, that string will be used as the custom field/column name for the `createdAt` attribute.


### `autoUpdatedAt`

```javascript
autoUpdatedAt: true
```
If set to `false`, this disables the automatic definition of an `updatedAt` attribute in your model.  By default, `updatedAt` is an attribute which will be automatically set with the current (timezone-agnostic) timestamp every time a record is updated.  If set to a string, that string will be used as the custom field/column name for the `updatedAt` attribute.

### tableName

```javascript
tableName: 'some_preexisting_table'
```

You can define a custom name for the physical collection in your adapter by adding a `tableName` attribute. __This isn't just for tables__.  In MySQL, PostgreSQL, Oracle, etc. this setting refers to the name of the table, but in MongoDB or Redis, it refers to the collection, and so forth. If no tableName is specified, Waterline will use the model's `identity` as its `tableName`.

This is particularly useful for working with pre-existing/legacy databases.

<!-- in WL2, this is `cid` (but is backwards-compatible) -->



### `attributes`

```js
attributes: {
  name: { type: 'string' },
  email: { type: 'email' },
  age: { type: 'integer' }
}
```

See [Attributes](http://sailsjs.com/documentation/concepts/ORM/Attributes.html).




<docmeta name="displayName" value="Model Settings">
