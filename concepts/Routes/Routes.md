# Routes
### Обзор

Самая основная функция любого веб-приложения - возможность интерпретировать запрос, отправленный по URL-адресу, и затем отправить ответ.  Для этого, ваше приложение должно иметь возможность отличать один URL от другого.

Как и большинство веб-фреймворков, Sails предоставляет router(маршрутизатор): механизм соотнесения URL-ов на контроллеры и виды.  **Routes** это правило которое сообщает Sails что делать столкнувшись с входящим запросом.  Есть два основных типа маршрутов в Sails: **custom** (или "eявный") и **automatic** (или "неявный").


### Custom Routes

Sails позволяет вам создавать URL-адреса вашего приложения любым способом - нет ограничений на фреймворк.

Every Sails проект поставляется с [`config/routes.js`](http://sailsjs.com/documentation/reference/sails.config/sails.config.routes.html), простым [Node.js модулем](http://nodejs.org/api/modules.html) который экспортирует объект пользовательского, или "явного" **routes**. Например, этот файл `routes.js` определяет шесть маршрутов; некоторые из них указывают на controller's action, другие прямо на view.

```javascript
// config/routes.js
module.exports.routes = {
  'get /signup': { view: 'conversion/signup' },
  'post /signup': 'AuthController.processSignup',
  'get /login': { view: 'portal/login' },
  'post /login': 'AuthController.processLogin',
  '/logout': 'AuthController.logout',
  'get /me': 'UserController.profile'
}
```


Каждый **route** содержит **address** (слева, т.е. `'get /me'`) и **target** (справа, т.е. `'UserController.profile'`)  **address** это  URL путь и (опционально) определенный [HTTP метод](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods). А **target** может быть определено несколькими различными способами ([смотрите раздел расширенных концепций on the subject](http://sailsjs.com/documentation/concepts/Routes/RouteTargetSyntax.html)), Но два разных синтаксиса, описанных выше, являются наиболее распространенными.  Когда Sails пинимает входящий запрос, он проверяет **address** всех пользовательских маршрутов на совпадение.  Если соответствующий маршрут найден, запрос передается его **target**.

НАпример, мы могли бы прочитать `'get /me': 'UserController.profile'` как:

> "Эй Sails, если получишь запрос GET на `http://mydomain.com/me`, запусти `profile` action из `UserController`, ок?"

Что делать, если я хочу изменить view layout для маршрута?  Нет проблем:

```javascript
'get /privacy': {
    view: 'users/privacy',
    locals: {
      layout: 'users'
    }
  },
```

#### Notes
+ Just because a request matches a route address doesn't necessarily mean it will be passed to that route's target _directly_.  For instance, HTTP requests will usually pass through some [middleware](http://sailsjs.com/documentation/concepts/Middleware) first.  And if the route points to a controller [action](http://sailsjs.com/documentation/concepts/Controllers?q=actions), the request will need to pass through any configured [policies](http://sailsjs.com/documentation/concepts/Policies) first.  Finally, there are a few special [route options](http://sailsjs.com/documentation/concepts/Routes/RouteTargetSyntax.html?q=route-target-options) which allow a route to be "skipped" for certain kinds of requests.
+ The router can also programmatically **bind** a **route** to any valid route target, including canonical Node middleware functions (i.e. `function (req, res, next) {}`).  However, you should always use the conventional [route target syntax](http://sailsjs.com/documentation/concepts/Routes/RouteTargetSyntax.html) when possible- it streamlines development, simplifies training, and makes your app more maintainable.



### Automatic Routes

In addition to your custom routes, Sails binds many routes for you automatically.  If a URL doesn't match a custom route, it may match one of the automatic routes and still generate a response.  The main types of automatic routes in Sails are:

* [Blueprint routes](http://sailsjs.com/documentation/reference/blueprint-api?q=blueprint-routes), which provide your [controllers](http://sailsjs.com/documentation/concepts/Controllers) and [models](http://sailsjs.com/documentation/concepts/ORM/Models.html) with a full REST API.
* [Assets](http://sailsjs.com/documentation/concepts/Assets), such as images, Javascript and stylesheet files.
* [CSRF](http://sailsjs.com/documentation/concepts/Security/CSRF.html), if turned on, provides a **/csrfToken** route to your app that can be used to retrieve the CSRF token.


### Supported Protocols

The Sails router is "protocol-agnostic"; it knows how to handle both [HTTP requests](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) and messages sent via [WebSockets](http://en.wikipedia.org/wiki/Websockets). It accomplishes this by listening for Socket.io messages sent to reserved event handlers in a simple format, called JWR (JSON-WebSocket Request/Response).  This specification is implemented and available out of the box in the [client-side socket SDK](http://sailsjs.com/documentation/reference/websockets/sails.io.js).



#### Notes
+ Advanced users may opt to circumvent the router entirely and send low-level, completely customizable WebSocket messages directly to the underlying Socket.io server.  You can bind socket events directly in your app's [`onConnect`](http://sailsjs.com/documentation/reference/sails.config/sails.config.sockets.html?q=commonlyused-options) function (located in [`config/sockets.js`](http://sailsjs.com/documentation/anatomy/myApp/config/sockets.js.html).)  But bear in mind that, in most cases, you are better off leveraging the request interpreter for socket communication - maintaining consistent routes across HTTP and WebSockets helps keep your app maintainable.




<docmeta name="displayName" value="Routes">
