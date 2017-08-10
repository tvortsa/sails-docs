# Locals

Переменные, доступные в определенном  view называют `locals`.  Locals представляют server-side данные которые _accessible_ в вашем view-- locals на самом деле не _included_ в скомпилированный HTML сли вы явно не ссылаетесь на них с помощью специального синтаксиса предоставляемого вашим view engine.

```ejs
<div>Logged in as <a><%= name %></a>.</div>
```

### Использование locals в вашем views

Нотация для доступа к locals в разных view engines различается.  В EJS, вы используете специальный template markup ( `<%= someValue %>`) для включения locals в ваши views.

Вот три случая template tags в EJS:
+ `<%= someValue %>`
  + HTML-экранирует  `someValue` local, и затем включает его как строк.
+ `<%- someRawHTML %>`
  + Includes the `someRawHTML` local verbatim, without escaping it.
  + Be careful!  This tag can make you vulnerable to XSS attacks if you don't know what you're doing.
+ `<% if (!loggedIn) { %>  <a>Logout</a>  <% } %>`
  + Запускает JavaScript внутри `<% ... %>` когда view компилируется.
  + Полезно для условий (`if`/`else`), и обхода данных в цикле (`for`/`each`).


Вот пример view (`views/backOffice/profile.ejs`) использующего 2 locals, `user` и `corndogs`:

```ejs
<div>
  <h1><%= user.name %>'s first view</h1>
  <h2>My corndog collection:</h2>
  <ul>
    <% _.each(corndogs, function (corndog) { %>
    <li><%= corndog.name %></li>
    <% }) %>
  </ul>
</div>
```

> Возможно, вы заметили еще один local, `_`.  По-умолчанию, Sails ередает несколько locals в ваш views автоматически, включая lodash (`_`).

Если данные, которые вы хотели бы передать в этот view полностью статичны, вам необязателен controller- можно просто hard-code ваш view и его locals в вашем файле `config/routes.js` , типа:

```javascript
  // ...
  'get /profile': {
    view: 'backOffice/profile',
    locals: {
      user: {
        name: 'Frank',
        emailAddress: 'frank@enfurter.com'
      },
      corndogs: [
        { name: 'beef corndog' },
        { name: 'chicken corndog' },
        { name: 'soy corndog' }
      ]
    }
  },
  // ...
```

С другой стороны, в более вероятном сценарии, эти данные являются динамическими, нам нужно будет использовать действие контроллера, чтобы загрузить данные из наших моделей, и затем передать их во view используя метод [res.view()](http://sailsjs.com/documentation/reference/res/res.view.html).

Предполагая, что мы подключили наш маршрут к одному из действий нашего контроллера (и наши модели были настроены), мы могли бы отправить наш view таким образом:

```javascript
// в api/controllers/UserController.js...

  profile: function (req, res) {
    // ...
    return res.view('backOffice/profile', {
      user: theUser,
      corndogs: theUser.corndogCollection
    });
  },
  // ...
```

### Экранирование ненадежных данных используя `exposeLocalsToBrowser`

Часто желательно чтобы данные &ldquo;bootstrap&rdquo; на страницу, чтобы it&rsquo;s available via Javascript as soon as the page loads, instead of having to fetch the data in a separate AJAX or socket request.  Sites like [Twitter and GitHub](https://blog.twitter.com/2012/improving-performance-on-twittercom) rely heavily on this approach in order to optimize page load times and provide an improved user experience.

In the past, a common way of solving this problem was with hidden form fields, or by hand-rolling code that injects server-side locals directly into a client-side script tag.  However, these techniques can present a challenge when some of the data you want to bootstrap is from an _untrusted_ source, meaning that it might contain HTML tags and Javascript code meant to compromise your app with an <a href="https://en.wikipedia.org/wiki/Cross-site_scripting" target="_blank">XSS attack</a>.  To help prevent such issues, Sails provides a helper function called `exposeLocalsToBrowser` that you can use in your views to output data safely.

To use `exposeLocalsToBrowser`, simply call it from within your view using the _non-escaping syntax_ for your template language.  For example, using the default EJS view engine, you would do:

```ejs
<%- exposeLocalsToBrowser() %>
```

По умолчанию, это представит _все_ locals ваших view как глобальные переменные `window.SAILS_LOCALS`.  Например, если код вашего action содержит:

```javascript
res.view('myView', {
  someString: 'hello',
  someNumber: 123,
  someObject: { owl: 'hoot' },
  someArray: [1, 'boot', true],
  someBool: false
  someXSS: '<script>alert("все ваши кредитные карты принадлежат нам");</script>'
});
```

то используя `exposeLocalsToBrowser` как показано выше, приведет к тому что locals будут безопасно загружаться таким образомthat `window.SAILS_LOCALS.someArray` будет содержать массив `[1, 'boot', true]`, и  `window.SAILS_LOCALS.someXSS` будет содержать массив _string_ `<script>alert("все ваши кредитные карты принадлежат нам");</script>`, не заставляя этот код фактически выполняться на странице.

Функция `exposeLocalsToBrowser` имеет единственный параметр `options` parameter который можно использовать для настройки вывода данных, and how.  Параметр `options` это словарь содержащий следующие свойства:

|&nbsp;   |     Property        | Type                                         | Default| Details                            |
|---|:--------------------|----------------------------------------------|:-----------------------------------|-----|
| 1 | _keys_     | ((array?))                              | `undefined` | A &ldquo;whitelist&rdquo; of locals to expose.  If left undefined, _all_ locals will be exposed.  If specified, this should be an array of property names from the locals dictionary.  For example, given the `res.view()` statement shown above, setting `keys: ['someString', 'someBool']` would cause `windows.SAILS_LOCALS` to be set to `{someString: 'hello', someBool: false}`.
| 2 | _namespace_ | ((string?)) | `SAILS_LOCALS` | The name of the global variable to assign the bootstrapped data to.
| 3| _dontUnescapeOnClient_ | ((boolean?)) | false | **Advanced. Not recommended for most apps.** If set to `true`, any string values that were escaped to avoid XSS attacks will _still be escaped_ when accessed from client-side JS, instead of being transformed back into the original value.  For example, given the `res.view()` statement from the example above, using `exposeLocalsToBrowser({dontUnescapeOnClient: true})` would cause `window.SAILS_LOCALS.someXSS` to be set to `&lt;script&gt;alert(&#39;hello!&#39;);`.


<docmeta name="displayName" value="Locals">
