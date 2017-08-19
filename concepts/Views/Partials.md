# Partials

При использовании стандартного view engine (`ejs`), Sails поддерживает использование _partials_ (или "view partials").  Partials в основном просто views разработанные чтобы быть использованными с другими views.

Они особенно полезны для повторного использования одинаковой разметки между различными views, layouts, и даже другими partials.

```ejs
<%- partial('./partials/navbar.ejs') %>
```

В зависимости от того, где рендерится partial располагается в `views/partials/navbar.ejs`, Который может выглядеть примерно так:

```ejs
<%
/**
 * views/partials/navbar.ejs
 *
 * > Note: Этот EJS комментарий не появится в ejs обслуживаемом браузером.
 * > Таким образом, вы можете быть настолько подробным, насколько вам нравится.  
 * > Просто будьте осторожны not to inadvertently
 * > type a percent sign followed by a greater-than sign (it'll bust you out of
 * > the EJS block).
 *
 */%>
<div class="navbar">
  <a href="/">Dashboard</a>
  <a href="/inbox">Inbox</a>
</div>
```


Целевой путь, который вы передаете как первый аргумент в `partial()` должен быть относительно того view, layout, или partial в котором вы его вызываете.  So if you are calling `partial()` from within a view file located at `views/pages/dashboard/user-profile.ejs`, and want to load `views/partials/widget.ejs` then you would use:

```ejs
<%- partial('../../partials/navbar.ejs') %>
```

### Partials и view locals

Partials автоматически наследуют view locals которые доступны везде, где они используются.  Например, если вы вызовите `partial()` с view в котором есть переменная `currentUser` то `currentUser`будет доступени в partial:

```ejs
<%
/**
 * views/partials/navbar.ejs
 *
 *  navbar сверху страницы.
 *
 * @needs {Dictionary} currentUser
 *   @property {Boolean} isLoggedIn
 *   @property {String} username
 */%>
<div class="navbar">
  <div class="links">
    <a href="/">Dashboard</a>
    <a href="/inbox">Inbox</a>
  </div>
  <div class="login-or-signup"><%
  // если пользователь имеет доступ к этой странице то он залогинен...
  if (currentUser.isLoggedIn) {
  %><span>
    Вы вошли как <a href="/<%= currentUser.username %>"><%= currentUser.username %></a>.
  </span><%
  }
  // В противном случае пользователь, обращающийся к этой странице, должен быть посетителем:
  else {
  %><span>
    <a href="/login">Log in</a>
  </span><%
  }
  %>
  </div>
</div>
```


### Overriding locals в partial

Автоматическое наследование view locals заботится о большинстве случаев использования для partials.  Но иногда, вы можете захотеть передать дополнительные динамические данные. На пример, Представьте себе, что ваше приложение имеет дубликаты копий следующего кода в несколько разных views:

```ejs
<%
// Список всех currently-logged в user's inbox.
%><ul class="message-list"><%
  // Отображаем каждое сообщение, с кнопкой удаления.
  _.each(messages, function (message) {
  %><li class="inbox-message" data-id="<%= message.id %>">
    <a href="/messages/<%= message.id %>"><%= message.subject %></a>
    <button class="fa fa-trash" is="delete-btn"></button>
  </li><% });
 %></ul>
```

Чтобы реорганизовать это, вы можете экстраполировать `<li>` в partial чтобы избежать дублирования кода.  Но если мы это сделаем, _мы не можем полагаться на автоматическое наследование_.  Partials наследуют только locals которые доступны для view, partial, или layout в котором их вызвали as a whole, но это `<li>` зависит от переменной  `message`, которая приходит из вызова [`_.each()`](https://lodash.com/docs/3.10.1#forEach).

К счастью, Sails также позволяет вам передавать дополнительный словарь (как plain JavaScript объект) переопределений как второй аргумент в `partial()`:

```
<%- partial(relPathToPartial, optionalOverrides) %>
```

These overrides will be accessible in the partial as local variables, where they will take precedence over any automatically inherited locals with the same variable name.

Here's our example from above, refactored to take advantage of this:

```ejs
<%
// A list representing the currently-logged in user's inbox.
%><ul class="message-list"><%
  // Display each message, with a button to delete it.
  _.each(messages, function (message) { %>
  <%- partial ('../partials/inbox-message.ejs', { message: message }) %>
  <% });
%></ul>
```


And finally, here is our new partial representing an individual inbox message:

```ejs
/**
 * views/partials/inbox-message.ejs
 *
 * An individual inbox message.
 *
 * @needs {Dictionary} message
 *   @property {Number} id
 *   @property {String} subject
 *
 */%>
<li class="inbox-message" data-id="<%= message.id %>">
  <a href="/messages/<%= message.id %>"><%= message.subject %></a>
  <button class="fa fa-trash" is="delete-btn"></button>
</li>
```







### Notes

> + Partials are rendered synchronously, so they will block Sails from serving more requests until they're done loading.  It's something to keep in mind while developing your app, especially if you anticipate a large number of connections.
> + Built-in support for partials in Sails is only for the default view engine, `ejs`.  If you decide to customize your Sails install and use a view engine other than `ejs`, then please be aware that support for partials (sometimes known as "blocks", "includes", etc.) may or may not be included, and that the usage will vary.  Refer to the documentation for your view engine of choice for more information on its syntax and conventions.


<docmeta name="displayName" value="Partials">

