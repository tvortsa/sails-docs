# Layouts

При создании приложения со множеством разных страниц, полезно экстраполировать разметку, разделяемую несколькими файлами HTML, в макет.  Это [сократит объем кода](http://en.wikipedia.org/wiki/Don't_repeat_yourself) in your project and helps you avoid making the same changes in multiple files down the road.

В Sails и Express, layouts также реализуются черех view engines.  Например, `jade` имеет собственную систему layout, со своим синтаксисом.

Для удобства, Sails имеет специальную поддержку макетов **при использовании дефолтного view engine, EJS**. If you'd like to use layouts with a different view engine, check out [that view engine's documentation](http://sailsjs.com/documentation/concepts/Views/ViewEngines.html) to find the appropriate syntax.


### Создание макетов

Sails макеты это специальные `.ejs` файлы в папке `views/` вашего приложения которые можно использовать для "обвертывания" или "вкладывания" других views. Layouts обычно содержат preamble (e.g. `<!DOCTYPE html><html><head>....</head><body>`) и conclusion (`</body></html>`).  А исходный файл view включается с использованием `<%- body %>`.  Layouts никогда не используют без view- .

Поддержку макетов в вашем приложении можно конфигурировать или отключить [`config/views.js`](http://sailsjs.com/documentation/anatomy/myApp/config/views.js.html), И может быть переопределено для конкретного маршрута или действия путем установки специального [local](http://sailsjs.com/documentation/concepts/Views/Locals.html) называемого `layout`. По-умолчанию, Sails будет компилировать все views используя layout расположенный в `views/layout.ejs`.

Чтобы указать, какой макет использует view, см. пример ниже. There is more information in the docs at [routes](http://sailsjs.com/documentation/concepts/Routes.html).

Пример маршрута ниже будет использовать view расположенный в `./views/users/privacy.ejs` с layout расположенным в `./views/users.ejs`

```javascript
'get /privacy': {
    view: 'users/privacy',
    locals: {
      layout: 'users'
    }
  },
```

Примерный controller action ниже будет использован для view расположенном в `./views/users/privacy.ejs` с макетом расположенным в `./views/users.ejs`

```javascript
privacy: function (req, res) {
  res.view('users/privacy', {layout: 'users'})
}
```

### Заметка

> #### Почему макет работает только с EJS?
> A couple of years ago, built-in support for layouts/partials was deprecated in Express. Instead, developers were expected to rely on the view engines themselves to implement this features. (See https://github.com/balderdashy/sails/issues/494 for more info on that.)
>
> Sails supports the legacy `layouts` feature for convenience, backwards compatibility with Express 2.x and Sails 0.8.x apps, and in particular, familiarity for new community members coming from other MVC frameworks. As a result, layouts have only been tested with the default view engine (ejs).
>
> If layouts aren&rsquo;t your thing, or (for now) if you&rsquo;re using a server-side view engine other than ejs, (e.g. Jade, handlebars, haml, dust) you&rsquo;ll want to set `layout:false` in [`sails.config.views`](http://sailsjs.com/documentation/reference/sails.config/sails.config.views.html), then rely on your view engine&rsquo;s custom layout/partial support.





<docmeta name="displayName" value="Layouts">
