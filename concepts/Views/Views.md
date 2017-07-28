# Views
### Обзор

В Sails, views (виды, представления) это шаблоны разметки, которые компилируются _на сервере_ в HTML страницы.  В большинстве случаев, views используются как ответы на входящие HTTP запросы, т.е. для обслуживания вашей домашней страницы.

Альтернативно, view может быть скомпилирован прямо в HTML строку для использования в вашем коде бэкэнда (см [`sails.renderView()`](https://github.com/balderdashy/sails-docs/blob/master/PAGE_NEEDED.md).)  Например, для отсылки HTML имэйлов, или сборки большой XML строки for use with a legacy API.


##### Создание view

По умолчанию, Sails сконфигурирован использовать EJS ([Embedded Javascript](http://embeddedjs.com/)) как движок для view.  Синтаксис EJS очень условный- если вы работали с php, asp, erb, gsp, jsp, etc., вы сразу же узнаете как он работает.

Если вы предпочитаете использовать другой view engine, there are a multitude of options.  Sails supports all of the view engines compatible with [Express](https://github.com/balderdashy/sails-docs/blob/master/PAGE_NEEDED.md) via [Consolidate](https://github.com/visionmedia/consolidate.js/).

Views объявляются в папке вашего приложения [`views/`](http://sailsjs.com/documentation/anatomy/myApp/views) по умолчанию, но как и все дефолтные пути в Sails, they are [configurable](https://github.com/balderdashy/sails-docs/blob/master/PAGE_NEEDED.md).  Если вам не надо обслуживать динамические HTML страницы (скажем, если вы делаете API для мобильного приложения), можете удалить эту папку из своего приложения.


##### Компиляция view

Вы можете обращаться к объекту `res` отовсюду (т.e. controller action, custom response, или policy), можете использовать [`res.view`](http://sailsjs.com/documentation/reference/res/res.view.html) для компиляции ваших views, затем отошлите итоговый HTML пользователю.

Вы также можете подключить view прямо в route в вашем файле `routes.js`.  Просто укжите соответствующий путь к view из папки `views/` вашего приложения.  Например:

```javascript
{
  'get /': {
    view: 'homepage'
  },
  'get /signup': {
    view: 'signupFlow/basicInfo'
  },
  'get /signup/password': {
    view: 'signupFlow/chooseAPassword'
  },
  // и т.д.
}
```

##### Что насчет одно-страничных приложений?

If you are building a web application for the browser, part (or all) of your navigation may take place on the client; i.e. instead of the browser fetching a new HTML page each time the user navigates around, the client-side code preloads some markup templates which are then rendered in the user's browser without needing to hit the server again directly.

In this case, you have a couple of options for bootstrapping the single-page app:

+ Use a single view, e.g. `views/publicSite.ejs`.  Advantages:
  + You can use the view engine in Sails to pass data from the server directly into the HTML that will be rendered on the client.  This is an easy way to get stuff like user data to your client-side javascript, without having to send AJAX/WebSocket requests from the client.
+ Use a single HTML page in your assets folder , e.g. `assets/index.html`. Advantages:
  + Although you can't pass server-side data directly to the client this way, this approach allows you to further decouple the client and server-side parts of your application.
  + Anything in your assets folder can be moved to a static CDN (like Cloudfront or CloudFlare), allowing you to take advantage of that provider's geographically distributed data centers to get your content closer to your users.



<docmeta name="displayName" value="Views">
