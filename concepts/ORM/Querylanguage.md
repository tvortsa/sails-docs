# язык запросов Waterline

Язык запросов Waterline это объектно-ориентированный синтаксис использующийся дял извлечения записей из любой, поддерживаемой БД. Внутри, Waterline использует database adapter(s) установленные в ваш проект для перевода этого языка в нативные запросы, и последующей отправки этих запросов в соответствующую БД.  Это означает, что вы можете использовать те-же запросы и в MySQL и в Redis, или MongoDb. И это позволяет вам изменять свою базу данных с минимальными (if any) изменениями кода приложения.


### Основы языка запросов

Объекты критериев формируются с использованием одного из четырех типов объектных ключей. Это верхний уровень ключи, используемые в объекте запроса. Они основаны на критериях, используемых в MongoDB, с небольшими вариациями.

Queries могут быть построены с использованием ключевого слова `where` и указания атрибутов, который позволит вам также использовать параметры запроса, такие как `limit` и `skip` или, если `where` исключен, весь объект будет рассматриваться как `where` критерий.

```javascript

Model.find({
  name: 'mary'
}).exec(function (err, peopleNamedMary){

});


// или


Model.find({
  where: { name: 'mary' },
  skip: 20,
  limit: 10,
  sort: 'createdAt DESC'
}).exec(function(err, thirdPageOfRecentPeopleNamedMary){

});
```

#### Пары ключей

Пара ключей может использоваться для поиска записей для значений, соответствующих точно указанному. Это база объекта критериев, где ключ представляет собой атрибут модели, а значение представляет собой строгую проверку соответствия записей для сопоставления значений.

```javascript
Model.find({
  name: 'lyra'
}).exec(function (err, peopleNamedLyra) {

});
```

Они могут использоваться вместе для поиска нескольких атрибутов.

```javascript
Model.find({
  name: 'walter',
  state: 'new mexico'
}).exec(function (err, waltersFromNewMexico) {

});
```

#### Complex Constraints

Complex constraints also have model attributes for keys but they also use any of the supported criteria modifiers to perform queries where a strict equality check wouldn't work.

```javascript
Model.find({
  name : {
    'contains' : 'yra'
  }
})
```

#### модификатор In

Provide an array to find records whose value for this attribute exactly matches _any_ of the specified search terms.

> This is more or less equivalent to "IN" queries in SQL, and the `$in` operator in MongoDB.

```javascript
Model.find({
  name : ['walter', 'skyler']
}).exec(function (err, waltersAndSkylers){

});
```

#### модификатор Not-In

Provide an array wrapped in a dictionary under a `!` key (like `{ '!': [...] }`) to find records whose value for this attribute _ARE NOT_ exact matches for any of the specified search terms.

> This is more or less equivalent to "NOT IN" queries in SQL, and the `$nin` operator in MongoDB.

```javascript
Model.find({
  name: { '!' : ['walter', 'skyler'] }
}).exec(function (err, everyoneExceptWaltersAndSkylers){

});
```

#### предикат Or 

Use the `or` modifier to match _any_ of the nested rulesets you specify as an array of query pairs.  For records to match an `or` query, they must match at least one of the specified query modifiers in the `or` array.

```javascript
Model.find({
  or : [
    { name: 'walter' },
    { occupation: 'teacher' }
  ]
}).exec(function(err, waltersAndTeachers){

});
```

### Criteria Modifiers

The following modifiers are available to use when building queries.

* `'<'`
* `'<='`
* `'>'`
* `'>='`
* `'!='`
* `'nin'`
* `'in'`
* `'like'`
* `'contains'`
* `'startsWith'`
* `'endsWith'`

> Note that the availability and behavior of the criteria modifiers when matching against attributes with [JSON attributes](http://sailsjs.com/documentation/concepts/models-and-orm/validations#?builtin-data-types) may vary according to the database adapter you&rsquo;re using.  For instance, while `sails-postgresql` will map your JSON attributes to the <a href="https://www.postgresql.org/docs/9.4/static/datatype-json.html" target="_blank">JSON column type</a>, you&rsquo;ll need to [send a native query](http://sailsjs.com/documentation/reference/waterline-orm/datastores/send-native-query) in order to query those attributes directly.  On the other hand, `sails-mongo` supports queries against JSON-type attributes, but you should be aware that if a field contains an array, the query criteria will be run against every _item_ in the array, rather than the array itself (this is based on the behavior of MongoDB itself).

#### '<'

Searches for records where the value is less than the value specified.

```javascript
Model.find({ age: { '<': 30 }})
```

#### '<='

Searches for records where the value is less or equal to the value specified.

```javascript
Model.find({ age: { '<=': 21 }})
```

#### '>'

Searches for records where the value is more than the value specified.

```javascript
Model.find({ age: { '>': 18 }})
```

#### '>='

Searches for records where the value is more or equal to the value specified.

```javascript
Model.find({ age: { '>=': 21 }})
```

#### '!='

Searches for records where the value is not equal to the value specified.

```javascript
Model.find({
  name: { '!=': 'foo' }
})
```

#### 'in'

Searches for records where the value is in the list of values.

```javascript
Model.find({
  name: { 'in': ['foo', 'bar'] }
})
```

#### 'nin'

Searches for records where the value is NOT in the list of values.

```javascript
Model.find({
  name: { 'nin': ['foo', 'bar'] }
})
```

#### 'contains'

Searches for records where the value for this attribute _contains_ the given string.

```javascript
Model.find({
  subject: { contains: 'music' }
}).exec(function (err, musicCourses){

});
```

#### 'startsWith'

Searches for records where the value for this attribute _starts with_ the given string.

```javascript
Model.find({
  subject: { startsWith: 'american' }
}).exec(function (err, coursesAboutAmerica){

});
```

#### 'endsWith'

Searches for records where the value for this attribute _ends with_ the given string.

```javascript
Model.find({
  subject: { endsWith: 'history' }
}).exec(function (err, historyCourses) {

})
```

#### 'like'

Searches for records using pattern matching with the `%` sign.

```javascript
Model.find({ food: { 'like': '%beans' }})
```

#### 'Date Ranges'

You can do date range queries using the comparison operators.

```javascript
Model.find({ date: { '>': new Date('2/4/2014'), '<': new Date('2/7/2014') } })
```

### Query Options

Query options allow you refine the results that are returned from a query. The current options
available are:

* `limit`
* `skip`
* `sort`

#### Limit

Limits the number of results returned from a query.

```javascript
Model.find({ where: { name: 'foo' }, limit: 20 })
```

> Note: if you set `limit` to 0, the query will always return an empty array.

#### Skip

Returns all the results excluding the number of items to skip.

```javascript
Model.find({ where: { name: 'foo' }, skip: 10 });
```

##### Pagination

`skip` and `limit` can be used together to build up a pagination system.

```javascript
Model.find({ where: { name: 'foo' }, limit: 10, skip: 10 });
```

`paginate` is a  Waterline helper method which can accomplish the same as `skip` and `limit`.

``` javascript
Model.find().paginate({page: 2, limit: 10});
```

> **Waterline**
>
> You can find out more about the Waterline API below:
> * [Sails.js Documentation](http://sailsjs.com/documentation/reference/waterline/queries)
> * [Waterline README](https://github.com/balderdashy/waterline/blob/master/README.md)
> * [Waterline Reference Docs](http://sailsjs.com/documentation/reference/waterline-orm)
> * [Waterline Github Repository](https://github.com/balderdashy/waterline)


#### Sort

Results can be sorted by attribute name. Simply specify an attribute name for natural (ascending)
sort, or specify an `asc` or `desc` flag for ascending or descending orders respectively.

```javascript
// Sort by name in ascending order
Model.find({ where: { name: 'foo' }, sort: 'name' });

// Sort by name in descending order
Model.find({ where: { name: 'foo' }, sort: 'name DESC' });

// Sort by name in ascending order
Model.find({ where: { name: 'foo' }, sort: 'name ASC' });

// Sort by object notation
Model.find({ where: { name: 'foo' }, sort: [{ 'name': 'ASC' }});

// Sort by multiple attributes
Model.find({ where: { name: 'foo' }, sort: [{ name:  'ASC'}, { age: 'DESC' }]);
```


<docmeta name="displayName" value="Query language">
