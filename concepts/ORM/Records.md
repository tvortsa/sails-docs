# Записи

Записи - _record_ это то, что вы получаете в ответ от `.find()` или `.findOne()`.  Каждая запись это уникально идентифицируемый объект который 1-к-1 соответствует записи в физической БД; т.е. строке в Oracle/MSSQL/PostgreSQL/MySQL, документу в MongoDB, или hash в Redis.

```js
Order.find().exec(function (err, records){
  if (err) {
    return res.serverError(err);
  }

  console.log('Found %d records', records.length);
  if (records.length > 0) {
    console.log('Found at least one record, and its `id` is:',records[0].id);
  }

  return res.ok();

});
```




### JSON сериализация

В Sails, записи это просто словари (plain JavaScript объекты), что означает, что они могут быть легко представлены как JSON. Но вы также можете настроить способ записи записей из определенной модели _stringified_ используя [`customToJSON` model setting](http://sailsjs.com/documentation/concepts/models-and-orm/model-settings#?customtojson).


### Populated values

Помимо основных данных атрибутов, таких как адреса электронной почты, телефонного номера, и даты рождения, Waterline может динамически хранить и извлекать связанные наборы записей [associations](http://sailsjs.com/documentation/concepts/models-and-orm/associations).  Где [`.populate()`](http://sailsjs.com/documentation/reference/waterline-orm/queries/populate) is called on a query, each of the resulting Записи будут содержать одно или несколько заполненных значений.  Каждое из этих заполненных значений представляет собой снимок записи (или массива записей) связанные с этой конкретной ассоциацией на момент запроса.

Тип заполняемого значения зависит от того, какая ассоциация:

+ `null`, или plain JavaScript объект,  _(если это соответствует "model" association)_ или
+ пустой массив, или массив plain JavaScript объектов _(если это соответствует ассоциации "collection")_



Например, предположим, что мы имеем дело с заказами очаровательных щенков-волков:

```js
Order.find()
.populate('buyers')  // a "collection" association
.populate('seller')  // a "model" association
.exec(function (err, orders){

  // этот массив это слепок Customers которые ассоциированы с первым Order как "buyers"
  orders[0].buyers;
  // => [ {id: 1, name: 'Robb Stark'}, {id: 6, name: 'Arya Stark'} ]

  // этот объект это слепок Company которая ассоциирована с первым Order как "seller"
  orders[0].seller;
  // => { id: 42941, corporateName: 'WolvesRUs Inc.' }

  // this array is empty because the second Order doesn't have any "buyers"
  orders[1].buyers;
  // => []

  // this is `null` because there is no "seller" associated with the second Order
  orders[1].seller;
  // => null
});
```

##### Expected types / values for association attributes

The table below shows what values you can expect in records returned from a `.find()` or `.findOne()` call under different circumstances.  

| &nbsp; |  without a `.populate()` added for the association | with `.populate()`, but no associated records found | with `.populate()`, with associated records found
|:--- |:--- | --- |:--- |
| Singular association (e.g. `seller`) | Whatever is in the database record for this attribute (typically `null` or a foreign key value) | `null` | A POJO representing a child record |
| Plural association (e.g. `buyers`) |  `undefined` (the key will not be present) | `[]` (an empty array) | An array of POJOs representing child records


##### Modifying populated values

To modify the populated values of a particular record or set of records, call the [.addToCollection()](http://sailsjs.com/documentation/reference/waterline-orm/models/add-to-collection), [.removeFromCollection()](http://sailsjs.com/documentation/reference/waterline-orm/models/remove-from-collection), or [.replaceCollection()](http://sailsjs.com/documentation/reference/waterline-orm/models/replace-collection) model methods.



<docmeta name="displayName" value="Records">
