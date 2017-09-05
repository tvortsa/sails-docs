# Routes

### Обзор

Базовой функцией web приложения является интерпретация запроса переданного через URL, и отсылка ответа (response).  Для этого, приложение должно иметь возможность отличать один URL от другого.

Как и в большинстве web фрэймворков, Sails предоставляет router: механизм соотношения URLs на контроллер и views.  **Routes** это правило которое говорит Sails что делать с входящим запросом.  Есть два основных типа routes в Sails: **custom** (или "явный") и **automatic** (или "неявный").


### Custom Routes

Sails позволяет вам создавать URLы вашего приложения без ограничений фрэймворка, как хотите.

Каждый Sails проект имеет [`config/routes.js`](http://sailsjs.com/documentation/reference/sails.config/sails.config.routes.html), это простой [Node.js module](http://nodejs.org/api/modules.html) который экспортирует объект custom, или "явный" **routes**. Например, вот файл `routes.js` объявляющий шесть маршрутов; некоторые из них указывают на action контроллера, другие прямо на view.

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


Каждый **route** состоит из **address** (слева, e.g. `'get /me'`) и **target** (справа, e.g. `'UserController.profile'`)  Где **address** это URL и (опционально) заданный [HTTP метод](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods). А **target** может быть определен несколькими различными способами ([см. раздел расширенных концепций по теме]https://github.com/tvortsa/sails-docs/blob/1.0/concepts/Routes/RouteTargetSyntax.md)), но два разных синтаксиса выше являются наиболее распространенными.  Когда Sails принимает входящий запрос, он проверяет **address** всех кастомных routes на совпадение.  Если совпадение найдено, запрос передается в его **target**.

Например, мы могли бы прочесть `'get /me': 'UserController.profile'` как:

> "Эй Sails, если получишь запрос GET по адресу `http://mydomain.com/me`, запусти действие `profile` контроллера `UserController`, хорошо?"

Что делать, если я хочу изменить макет представления в самом маршруте?  Нет проблемм:

```javascript
'get /privacy': {
    view: 'users/privacy',
    locals: {
      layout: 'users'
    }
  },
```

#### Notes
+ Просто потому, что запрос соответствует адресу маршрута, не обязательно означает, что он будет передан в целевой маршрут  _напрямую_.  Например, HTTP запросы обычно будут передаваться сперва через [middleware](http://sailsjs.com/documentation/concepts/Middleware).  И если маршрут указывает на контроллер [action](http://sailsjs.com/documentation/concepts/Controllers?q=actions),запрос сперва должен пройти через любой настроенный [policies](http://sailsjs.com/documentation/concepts/Policies). Наконец есть несколько специальных [route options](http://sailsjs.com/documentation/concepts/Routes/RouteTargetSyntax.html?q=route-target-options) которые позволяют маршруту "быть пропущенным" для определенных видов запросов.
+ Router может также программно **bind**  **route** к любой валидной route target, включая каноническую Node middleware functions (i.e. `function (req, res, next) {}`).  However, you should always use the conventional [route target syntax](http://sailsjs.com/documentation/concepts/Routes/RouteTargetSyntax.html) when possible- it streamlines development, simplifies training, and makes your app more maintainable.



### Automatic Routes

In addition to your custom routes, Sails binds many routes for you automatically.  If a URL doesn't match a custom route, it may match one of the automatic routes and still generate a response.  The main types of automatic routes in Sails are:

* [Blueprint routes](http://sailsjs.com/documentation/reference/blueprint-api?q=blueprint-routes), which provide your [controllers](http://sailsjs.com/documentation/concepts/Controllers) and [models](http://sailsjs.com/documentation/concepts/ORM/Models.html) with a full REST API.
* [Assets](http://sailsjs.com/documentation/concepts/Assets), such as images, Javascript and stylesheet files.


##### Unhandled Requests

If no custom or automatic route matches a request URL, Sails will send back a default 404 response.  This response can be customized by adding a `api/responses/notFound.js` file to your app.  See [custom responses](http://sailsjs.com/documentation/concepts/extending-sails/custom-responses) for more info.

##### Unhandled Errors in Request Handlers

If an unhandled error is thrown during the processing of a request (for instance, in some [action code](http://sailsjs.com/documentation/concepts/actions-and-controllers), Sails will send back a default 500 response. This response can be customized by adding a `api/responses/serverError.js` file to your app.  See [custom responses](http://sailsjs.com/documentation/concepts/extending-sails/custom-responses) for more info.

### Supported Protocols

The Sails router is "protocol-agnostic"; it knows how to handle both [HTTP requests](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) and messages sent via [WebSockets](http://en.wikipedia.org/wiki/Websockets). It accomplishes this by listening for Socket.io messages sent to reserved event handlers in a simple format, called JWR (JSON-WebSocket Request/Response).  This specification is implemented and available out of the box in the [client-side socket SDK](http://sailsjs.com/documentation/reference/websockets/sails.io.js).



#### Notes
+ Advanced users may opt to circumvent the router entirely and send low-level, completely customizable WebSocket messages directly to the underlying Socket.io server.  You can bind socket events directly in your app's [`onConnect`](http://sailsjs.com/documentation/reference/sails.config/sails.config.sockets.html?q=commonlyused-options) function (located in [`config/sockets.js`](http://sailsjs.com/documentation/anatomy/myApp/config/sockets.js.html).)  But bear in mind that, in most cases, you are better off leveraging the request interpreter for socket communication - maintaining consistent routes across HTTP and WebSockets helps keep your app maintainable.




<docmeta name="displayName" value="Routes">
