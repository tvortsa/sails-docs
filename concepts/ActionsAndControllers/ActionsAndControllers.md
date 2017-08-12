# Actions и controllers

### Обзор

_Actions_ это лавные объекты в вашем Sails приложении которые которые отвечают за реакцию на *запросы* от web браузера, мобильного приложения или любой другой системы способной взаимодействовать с сервером.  Они часто выполняют ролдь посредников между вашими [моделями](http://sailsjs.com/documentation/concepts/ORM/Models.html) и [views](http://sailsjs.com/documentation/concepts/Views). Для большинства приложений, actions будут содержать основную часть &rsquo;s [бизнес-логики](http://en.wikipedia.org/wiki/Business_logic) вашего проекта.

Actions связаны с [routes](http://sailsjs.com/documentation/concepts/Routes) в вашем приложении, так что когда клиент запрашивает маршрут, action выполняется для осуществления некоторой бизнес-логики и отправки ответа.  Например, `GET /hello` route в вашем приложении может быть связано с действием вроде:

```javascript
function (req, res) {
  return res.send('Hi there!');
}
```

Каждый раз, когда веб-браузер указывает на `/hello` URL вашего сервера приложений, отображается страница с: &ldquo;Hi there!&rdquo;.

### Где объявлять actions?
Actions объявляют в папке `api/controllers/` и подпапках (мы скоро поговорим о _controllers_ ). Чтобы файл был распознан как action, он должен быть _kebab-cased_ (содержать только символы нижнего регистра, числа и дефисы).  Ссылаясь на action в Sails (например, когда [связываем его с route](http://sailsjs.com/documentation/concepts/routes/custom-routes#?action-target-syntax)), спользуйте его путь относительно `api/controllers`, без расширения в имени файла.  Например, файл `api/controllers/user/find.js` представляет собой действие с идентификатором `user/find`.

##### Расширения файлов для actions

Action могут иметь любые расширения кроме `.md` (Markdown) and `.txt` (text).  По умолчанию, Sails знает только, как интерпретировать `.js` файлы, но вы можете кастомизировать это для использования например [CoffeeScript](http://sailsjs.com/documentation/tutorials/using-coffee-script) или [TypeScript](http://sailsjs.com/documentation/tutorials/using-type-script).

### Как выглядит файл action?

Файлы действий могут использовать один из двух форматов: _classic_ или _Actions2_.

##### Classic actions

Самый быстрый способ начать создание Sails action заключается в объявлении его как функции.  Когда клиент запрашивает маршрут, привязанный к этому действию, функция будет вызываться с использованием [request object](http://sailsjs.com/documentation/reference/request-req) в качестве персвого аргумента (обычно его называют именем `req`), и [response object](http://sailsjs.com/documentation/reference/response-res) как второй аргумент (обычно имя `res`).  Вот пример действия функции, которая ищет пользователя по ID, и либо отображает view «приветствия», либо перенаправляет на страницу регистрации, если пользователь не может быть найден:

```javascript
module.exports = function welcomeUser (req, res) {

   // Получаем параметр `userId` из запроса.
   // Это можно было бы задать в строке запроса, в
   // теле запроса, или как часть URL испльзуемого
   // для создания запроса.
   var userId = req.param('userId');

   // Если не задано `userId`, или это не было число, верните ошибку.
   if (!_.isNumeric(userId)) {
      return res.badRequest(new Error('ID пользователя не указан!'));
   }

   // Ищем пользователя, чей идентификатор был указан в запросе.
   User.findOne(userId).exec(function (err, user) {
      // Обработка неизвестных ошибок.
      if (err) {return res.serverError(err);}
      // Если пользователь не найден, перенаправляем на signup.
      if (!user) {return res.redirect('/signup');
      // Отображаем view приветствия, задавая переменную view
      // с именем "name" в значение имени пользователя.
      return res.view('welcome', {name: user.name});
   });

}
```

##### Actions2

Другой, более структурированный способ создания action это написание _machine_, создается почти так же, как Sails [helpers](http://sailsjs.com/documentation/concepts/helpers).  Определив свой action как machine, это, по сути, самодокументирование и самотестирование.  Вот то же действие, что и выше, переписанное с использованием машинного формата:

```javascript
module.exports = {

   friendlyName: 'Добро пожаловать',

   description: 'Ищем указанного пользователя и приветствуем его, или перенаправляем на страницу входа если пользователь не найден.',

   inputs: {
      userId: {
         description: 'The ID of the user to look up.',
         // By declaring a numeric example, Sails will automatically respond with `res.badRequest`
         // if the `userId` parameter is not a number.
         type: 'number',
         // By making the `userId` parameter required, Sails will automatically respond with
         // `res.badRequest` if it's left out.
         required: true
      }
   },

   exits: {
      success: {
         responseType: 'view',
         viewTemplatePath: 'welcome'
      },
      notFound: {
         description: 'No user with the specified ID was found in the database.',
         responseType: 'redirect'
      }
   },

   fn: function (inputs, exits, env) {

      // Look up the user whose ID was specified in the request.
      // Note that we don't have to validate that `userId` is a number;
      // the machine runner does this for us and returns `badRequest`
      // if validation fails.
      User.findOne(inputs.userId).exec(function (err, user) {

         // Handle unknown errors.
         if (err) {return exits.error(err);}

         // If no user was found, redirect to signup.
         if (!user) {return exits.notFound('/signup');}

         // Display the welcome view.
         return exits.success({name: user.name});
      });
   }
};
```

Sails использует модуль [machine-as-action](https://github.com/treelinehq/machine-as-action) для автоматического создания функций обработки маршрутов out of machines like the example above.  See the [machine-as-action docs](https://github.com/treelinehq/machine-as-action#customizing-the-response) for more information.

> Note that machine-as-action provides actions with access to the [request object](http://sailsjs.com/documentation/reference/request-req) as `env.req`, and to the Sails application object (in case you don&rsquo;t have [globals](http://sailsjs.com/documentation/concepts/globals) turned on) as `env.sails`.

Using classic `req, res` functions for your actions is the quickest way to start out with a new app.  However, using Actions2 provides several advantages:

 * The code you write is not directly dependent on `req` and `res`, making it easier to re-use or abstract into a [helper](http://sailsjs.com/documentation/concepts/helpers).
 * You guarantee that you&rsquo;ll be able to quickly determine the names and types of the request parameters the action expects, and you'll know that they will be automatically validated before the action is run.
 * You&rsquo;ll be able to see all of the possible outcomes from running the action without having to dissect the code.

In a nutshell, your code will be standardized in a way that makes it easier to re-use and modify later.

### Контроллеры

 Самый быстрый способ начать писать приложение Sails это организовать ваши actions в _файле контроллера_.  Это [_PascalCased_](https://en.wikipedia.org/wiki/PascalCase) файл имя которого должно заканчиваться на `Controller`, содержащий словарь actions.  Например, "User controller" может быть создан как файл `api/controllers/UserController.js` содержащий:

```javascript
module.exports = {
   login: function (req, res) { ... },
   logout: function (req, res) { ... },
   signup: function (req, res) { ... },
};
```

Вы можете использовать [`sails generate controller`](http://sailsjs.com/documentation/reference/command-line-interface/sails-generate#?sails-generate-controller-foo-action-1-action-2) для быстрого создания файла контроллера.

##### Расширение имени файлов контроллеров

Контроллер может иметь любое расширение кроме `.md` (Markdown) and `.txt` (text).  По умолчанию, Sails знает только как интерпретировать `.js` файлы, but you can customize your app to use things like [CoffeeScript](http://sailsjs.com/documentation/tutorials/using-coffee-script) or [TypeScript](http://sailsjs.com/documentation/tutorials/using-type-script) as well.


### Standalone actions

For larger, more mature apps, _standalone actions_ may be a better approach than controller files.  In this scheme, rather than having multiple actions living in a single file, each action is in its own file in an appropriate subfolder of `api/controllers`.  For example, the following file structure would be equivalent to the  `UserController.js` file:

```
api/
 controllers/
  user/
   login.js
   logout.js
   signup.js
```

where each of the three Javascript files exports a `req, res` function or an Actions2 definition.

Using standalone actions has several advantages over controller files:

* It's easier to keep track of the actions that your app contains, by simply looking at the files contained in a folder rather than scanning through the code in a controller file.
* Each action file is small and easy to maintain, whereas controller files tend to grow as your app grows.
* [Routing to standalone actions](http://sailsjs.com/documentation/concepts/routes/custom-routes#?action-target-syntax) in nested subfolders is more intuitive than with nested controller files (`foo/bar/baz.js` vs. `foo/BarController.baz`).

* Blueprint index routes apply to top-level standalone actions, so you can create an `api/controllers/index.js` file and have it automatically bound to your app&rsquo;s `/` route (as opposed to having to create an arbitrary controller file to hold the root action).


### Keeping it lean

In the tradition of most MVC frameworks, Sails recommends that your apps contain "thin" controllers -- that is, your action code should be as lean as possible, with any re-usable code moved into [helpers](http://sailsjs.com/documentation/concepts/helpers) or even extracted into node modules.  This makes your apps easier to maintain in the long term!

<docmeta name="displayName" value="Actions and controllers">
