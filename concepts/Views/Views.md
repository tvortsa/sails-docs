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

Если вы создаете web приложение для браузера, частично (или полностью) ваша навигация может располагаться на клиенте; i.e. Вместо того, чтобы браузер получал новую HTML страницу каждый раз когда пользоатель navigates around, Клиентский код предварительно загружает некоторые шаблоны разметки, которые затем отображаются в браузере пользователя, без необходимости снова ударять по серверу.

В таком случае, У вас есть несколько вариантов для загрузки одностраничного приложения:

+ Использование единственного view, e.g. `views/publicSite.ejs`.  преимущества:
  + Вы можете использовать view engine в Sails для передачи данных с сервера прямо в HTML которые будут отображаться на клиенте.  Это простой способ получить информацию, подобную пользовательским данным, на ваш клиентский javascript, без необходимости отсылать  AJAX/WebSocket запросы с клиента.
+ Использование единственного HTML page в вашей папке assets , e.g. `assets/index.html`. Преимущества:
  + Хотя вы не можете передавать серверные данные непосредственно клиенту таким образом, Этот подход позволяет вам дополнительно отделить клиентскую и серверную части приложения.
  + Anything in your assets folder can be moved to a static CDN (like Cloudfront or CloudFlare), allowing you to take advantage of that provider's geographically distributed data centers to get your content closer to your users.



<docmeta name="displayName" value="Views">
