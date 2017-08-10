# Макеты (Layouts)

При построении приложения с множеством разных страниц, бывает полезно собрать разметку которая используется несколькими HTML страницами в один layout.  Это [существенно сокращает общий объем кода](http://en.wikipedia.org/wiki/Don't_repeat_yourself) в вашем проекте и помогат избежать повторений многих изменений в разных файлах.

В Sails и Express, layouts реализуются самими системами view engines.  Например, `jade` меет собственную систему layout , со своим синтаксисом.

Для удобства, Sails связывает специальную поддержку макетов **при спользовании дефолтного view engine, EJS**. If you'd like to use layouts with a different view engine, check out [that view engine's documentation](http://sailsjs.com/documentation/concepts/Views/ViewEngines.html) to find the appropriate syntax.


### Создание Layouts

В Sails layouts это специальный `.ejs` файл в папке `views/` вашего приложения, который вы можете использовать для "wrap" или "sandwich" других views. Layouts обычно содержат преамбулу (т.е. `<!DOCTYPE html><html><head>....</head><body>`) и заканчиваются (`</body></html>`).  Оригинальный view файл будет помещаться в `<%- body %>`.  Layouts are never used without a view- that would be like serving someone a bread sandwich.

Layout support for your app can be configured or disabled in [`config/views.js`](http://sailsjs.com/documentation/anatomy/myApp/config/views.js.html), and can be overridden for a particular route or action by setting a special [local](http://sailsjs.com/documentation/concepts/Views/Locals.html) called `layout`. По умолчанию, Sails будет компилировать все views используя layout расположенный во `views/layout.ejs`.

Чтобы указать, какой макет использует вид, см пример ниже. Еще информация есть в [routes](http://sailsjs.com/documentation/concepts/Routes.html).

The example route below will use the view located at `./views/users/privacy.ejs` within the layout located at `./views/users.ejs`

```javascript
'get /privacy': {
    view: 'users/privacy',
    locals: {
      layout: 'users'
    }
  },
```

The example controller action below will use the view located at `./views/users/privacy.ejs` within the layout located at `./views/users.ejs`

```javascript
privacy: function (req, res) {
  res.view('users/privacy', {layout: 'users'})
}
```

### Notes

> #### Why do layouts only work for EJS?
> A couple of years ago, built-in support for layouts/partials was deprecated in Express. Instead, developers were expected to rely on the view engines themselves to implement this features. (See https://github.com/balderdashy/sails/issues/494 for more info on that.)
>
> Sails supports the legacy `layouts` feature for convenience, backwards compatibility with Express 2.x and Sails 0.8.x apps, and in particular, familiarity for new community members coming from other MVC frameworks. As a result, layouts have only been tested with the default view engine (ejs).
>
> If layouts aren&rsquo;t your thing, or (for now) if you&rsquo;re using a server-side view engine other than ejs, (e.g. Jade, handlebars, haml, dust) you&rsquo;ll want to set `layout:false` in [`sails.config.views`](http://sailsjs.com/documentation/reference/sails.config/sails.config.views.html), then rely on your view engine&rsquo;s custom layout/partial support.





<docmeta name="displayName" value="Layouts">
