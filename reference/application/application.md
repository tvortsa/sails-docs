# Application (`sails`)

Объект Sails application содержит все relevant runtime state для Sails приложения.
По-умолчанию он представлен глобально как `sails`, но это [можно отключить](http://sailsjs.com/documentation/reference/configuration/sails-config-globals), e.g. for
use cases where multiple Sails app instances need to exist at once, or where globals
are not an option. Объект приложения также всегда доступен через запрос (`req._sails`), и внутри [model](http://sailsjs.com/documentation/concepts/models-and-orm/models) и [service](http://sailsjs.com/documentation/concepts/services) через модуль `this.sails`.

### Как это работает?

Экземпляр приложения автоматически создается при первом запуске `require('sails')`.
Это то, что происходит в сгенерированном файле `app.js`:

```javascript
var sails = require('sails');
```




### Свойства

Объект приложения имеет ряд методов и свойств, которые полезны.
Официально поддерживаемые методы объекта `sails` раскрываются на других
страницах этого раздела.  Вот несколько из его самых полезных свойств:

##### sails.models

Словарь всех загруженных [Sails моделей](http://sailsjs.com/documentation/concepts/models-and-orm/models), индексированных по их _identity_.

По умолчанию, identity модели это lowercased версия ее имени, без расширения **.js**.  Например, default identity для модели загруженной как `api/models/PowerPuff.js` будет `powerpuff`, и она будет доступна через `sails.models.powerpuff`.  A model's identity can be customized by setting an `identity` property in its module file.

##### sails.config

Все настройки данного экземпляра Sails, loaded from a combination of environment variables, `.sailsrc` files, user-configuration files and defaults.  See the [configuration concepts section](http://sailsjs.com/documentation/concepts/configuration) for a full overview of configuring Sails, and the [configuration reference](http://sailsjs.com/documentation/reference/configuration) for details on individual options.

##### sails.sockets

A set of convenience methods for low-level interaction with connected websockets.  See the [`sails.sockets.*` reference section](http://sailsjs.com/documentation/reference/web-sockets/sails-sockets) for details.

##### sails.hooks

A dictionary of all loaded [Sails hooks](http://sailsjs.com/documentation/concepts/extending-sails/hooks), indexed by their _identity_.  Use `sails.hooks` to access properties and methods of hooks you've installed to extend Sails; for example, by calling `sails.hooks.email.send()`.  You can also use this dictionary to access the Sails [core hooks](http://sailsjs.com/documentation/concepts/extending-sails/hooks#?types-of-hooks), for advanced usage.

By default, a hook's identity is the lowercased version of its folder name, with any `sails-hook-` prefix removed.  For example, the default identity for a hook loaded from `node_modules/sails-hook-email` would be `email`, and the hook would be accessible via `sails.hooks.email`.  An installed hook's identity can be changed via the [`installedHooks` config property](http://sailsjs.com/documentation/concepts/extending-sails/hooks/using-hooks#?changing-the-way-sails-loads-an-installable-hook).

See the [hooks concept documentation](http://sailsjs.com/documentation/concepts/extending-sails/hooks) for more info about hooks.

##### `sails.io`

The API exposed by the [`sails.sockets.*` methods](http://sailsjs.com/documentation/reference/web-sockets/sails-sockets) is flexible enough out of the box to cover the requirements of most applications, and using them will future-proof your app against possible changes in the underlying implementation.  However, if you are working on bringing some legacy code from a vanilla Socket.io app into your Sails app, it can be useful to talk to Socket.io directly.  To accomplish this, Sails provides raw access to the underlying [socket.io](http://socket.io/) server instance (`io`) as `sails.io`. See the [Socket.io docs](http://socket.io/docs/) for more information.  If you decide to use Socket.io directly, please proceed with care.

> As of v0.11.4, Sails bundles `socket.io@v1.4.3` as a dependency of [sails-hook-sockets](github.com/balderdashy/sails-hook-sockets), a core hook.





### Creating a new application object (advanced)

If you are implementing something unconventional (e.g. writing tests for Sails core)
where you need to create more than one Sails application instance in a process, you _should not_ use
the instance returned by `require('sails')`, as this can cause unexpected behavior.  Instead, you should
obtain application instances by using the Sails constructor:

```javascript
var Sails = require('sails').constructor;
var sails0 = new Sails();
var sails1 = new Sails();
var sails2 = new Sails();
```

Each app instance (`sails0`, `sails1`, `sails2`) can be loaded/lifted separately,
using different configuration.

For more on using Sails programatically, see the conceptual overview on [programatic usage in Sails](http://sailsjs.com/documentation/concepts/programmatic-usage).


<docmeta name="displayName" value="Application">
