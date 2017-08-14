# Маршрутинг на actions

### Ручной роутинг

По умолчанию, controller actions в вашем приложении Sails недоступны для пользователей пока вы не _свяжете_ его с маршрутом в вашем [`config/routes.js` файле](http://sailsjs.com/documentation/reference/configuration/sails-config-routes).  Когда вы связываете маршрут, то укажите URL через который пользователь может получить доступ к action, а также опции типа [CORS security settings](http://sailsjs.com/documentation/concepts/security/cors#?configuring-cors-for-individual-routes).

Чтобы связать маршрут с действием в файле `config/routes.js`, можете использовать HTTP verb и path (i.e. the **route address**) как ключ, а объявление action identity как значение (i.e. the **route target**).

Например, следующий ручной маршрут заставит ваше приложение запускать action `makeIt`  в `api/controllers/SandwichController.js` всякий раз при получении POST запроса `/make/a/sandwich`:

```js
  'POST /make/a/sandwich': 'SandwichController.make'
```

Если вы используете standalone actions, so that you had an `api/controllers/sandwich/make.js` file, a more intuitive syntax exists which uses the path to the action (relative to `api/controllers`):

```js
  'POST /make/a/sandwich': 'sandwich/make'
```

For a full discussion of routing, please see the [routes documentation](http://sailsjs.com/documentation/concepts/Routes).

### Automatic routing

Sails can also automatically bind routes to your controller actions so that a `GET` request to `/:actionIdentity` will trigger the action.  This is called _blueprint action routing_, and it can be activated by setting `actions` to `true` in the [`config/blueprints.js`](http://sailsjs.com/documentation/reference/configuration/sails-config-blueprints) file.  For example, with blueprint action routing turned on, a `signup` action saved in `api/controllers/UserController.js` or `api/controllers/user/signup.js` would be bound to a `/user/signup` route.  See the [blueprints documentation](http://sailsjs.com/documentation/reference/blueprint-api) for more information about Sails&rsquo; automatic route binding.


<docmeta name="displayName" value="Routing to actions">
