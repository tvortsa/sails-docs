# Globals
### Обзор

Для удобства, Sails предоставляет полезные глобальные переменные.  По дефолту, в вашем приложениии [models](http://sailsjs.com/documentation/reference/Models), [services](http://sailsjs.com/documentation/reference/Services), и глобальный объект `sails` все доступны в глобальном scope; что означает, что вы можете ссылаться на них по имени в любом месте вашего бэкэнд-кода  (as long as Sails [has been loaded](https://github.com/balderdashy/sails/tree/master/lib/app)).

Nothing in Sails core relies on these global variables - each and every global exposed in Sails may be disabled in `sails.config.globals` (conventionally configured in `config/globals.js`.)


### Объект App (`sails`)
В большинстве случаев, вы захотите сохранить  глобально доступный объект `sails` - это делает код приложения намного чище.  However, if you _do_ need to disable _all_ globals, including `sails`, you can get access to `sails` on the request object (`req`).

### Models и Services
В вашем приложении [models](http://sailsjs.com/documentation/reference/Models) и [services](http://sailsjs.com/documentation/reference/Services) представлены как глобальные переменные используют их `globalId`.  Для примера, model объявленная в файле `api/models/Foo.js` будет глобально доступна как `Foo`, а service объявленный как `api/services/Baz.js` будет доступен как `Baz`.

### Async (`async`) и Lodash (`_`)
Sails также предоставляет экземпляр [lodash](http://lodash.com) как `_`, и экземпляр [async](https://github.com/caolan/async) как `async`.  Эти широко используемые утилиты предоставлены по умолчанию так что их не нужно импортировать через `npm install` .  Like any of the other globals in sails, they can be disabled.



<docmeta name="displayName" value="Globals">
