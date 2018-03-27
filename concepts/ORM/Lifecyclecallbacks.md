# Коллбэки жизненного цикла

### Обзор

Обратные вызовы жизненного цикла - это функции, которые автоматически вызываются до или после certain _model_ actions. Например, иногда мы используем lifecycle callbacks для автоматического хеширования пароля перед созданием или обновлением модели `Account`.

Sails предоставляет по умолчанию несколько обратных вызовов жизненного цикла.

Никакие обратные вызовы жизненного цикла не выполняются на объемных вставках данных, используя `createEach`.


##### Коллбэки в `create`

Коллбэк жизненного цикла `afterCreate` будут выполняться только по запросам, у которых `fetch` meta flag установлен в `true`. Об использовании флагов `meta` см. [Waterline Queries](http://sailsjs.com/documentation/reference/waterline-orm/queries/meta).

  - beforeCreate: fn(recordToInsert, cb)
  - afterCreate: fn(newlyInsertedRecord, cb)

##### Коллбэки в `update`

Коллбэк жизненного цикла `afterUpdate` будут выполняться только по запросам, у которых `fetch` meta flag установлен в `true`. Об использовании флагов `meta` flags see [Waterline Queries](http://sailsjs.com/documentation/reference/waterline-orm/queries/meta).

  - beforeUpdate: fn(valuesToUpdate, cb)
  - afterUpdate: fn(updatedRecord, cb)

##### Коллбэки в `destroy`

Коллбэк жизненного цикла `afterDestroy` будут выполняться только по запросам, у которых `fetch` meta flag установлен в `true`. For more information on using the `meta` flags see [Waterline Queries](http://sailsjs.com/documentation/reference/waterline-orm/queries/meta).

  - beforeDestroy: fn(criteria, cb)
  - afterDestroy: fn(destroyedRecord, cb)


### Пример

Если вы хотите хешировать пароль перед сохранением в базе данных, вы можете использовать коллбэк жизненного цикла `beforeCreate`.

```javascript
var bcrypt = require('bcrypt');

module.exports = {

  attributes: {

    username: {
      type: 'string',
      required: true
    },

    password: {
      type: 'string',
      minLength: 6,
      required: true,
      columnName: 'hashed_password'
    }

  },


  // Коллбэк жизненного цикла
  beforeCreate: function (values, cb) {

    // Хэш пароля
    bcrypt.hash(values.password, 10, function(err, hash) {
      if(err) return cb(err);
      values.password = hash;
      //calling cb() with an argument returns an error. Useful for canceling the entire operation if some criteria fails.
      cb();
    });
  }
};
```



<docmeta name="displayName" value="Lifecycle callbacks">
