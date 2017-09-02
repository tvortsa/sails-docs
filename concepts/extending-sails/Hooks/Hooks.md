# Hooks

### Что такое hook?

Нук это модуль Node который добавляет функционал в ядро Sails.  The [hook specification](http://sailsjs.com/documentation/concepts/extending-sails/hooks/hook-specification) определяет требования, предъявляемые модулем для Sails чтобы иметь возможность импортировать свой код и сделать доступными новые функции.  Поскольку они могут быть сохранены отдельно от ядра, хуки позволяют передавать код Sails между приложениями и разработчиками без изменения структуры.

### Hooks vs. Services

Hooks share some common features with Sails [services](http://sailsjs.com/documentation/concepts/Services).  They both allow developers to store commonly used code in one location, and they both make new methods available globally to a Sails app.  However, there are some key differences between the two concepts:

* Сервисы не могут быть сохранены независимо от приложения.  While some types of hooks may be tied to a single app (see [Project Hooks](http://sailsjs.com/documentation/concepts/extending-sails/Hooks/projecthooks.html)), other types can be developed independently of a Sails app and installed using `npm install`.
* Hooks have their own initialization system.  This allows them to be more dynamic and configure themselves when Sails lifts.
* Hooks can add new [routes](http://sailsjs.com/documentation/concepts/Routes) to a Sails app before it lifts.

Services are still a good choice for code that is shared between multiple [controllers](http://sailsjs.com/documentation/concepts/Controllers) or [models](http://sailsjs.com/documentation/concepts/models-and-orm/models) in an app, but
* is unlikely to be reused in another app
* won't need to behave differently in different environments (e.g. development vs. production)

For all other reusable code, hooks are the way to go!

### Типы хуков

В Sails доступно три типа хуков:

1. **Core hooks**.  These hooks provide many of the common features essential to a Sails app, such as request handling, blueprint route creation, and database integration via [Waterline](http://sailsjs.com/documentation/concepts/models-and-orm).  Core hooks are bundled with the Sails core and are thus available to every app.  Вам нечасто придется вызывать методы core hook в своем коде.
2. **Project hooks**.  Это хуки которые находятся в папке `api/hooks` приложения Sails.  Project hooks provide a way to take advantage of the features of the hook system for code that doesn&rsquo;t need to be shared between apps.
3. **Installable hooks**.  These hooks are installed into an app&rsquo;s `node_modules` folder using `npm install`.  Installable hooks allow developers in the Sails community to create &ldquo;plug-in&rdquo;-like modules for use in Sails apps.

### Read more

* [Using hooks in your app](http://sailsjs.com/documentation/concepts/extending-sails/Hooks/usinghooks.html)
* [The hook specification](http://sailsjs.com/documentation/concepts/extending-sails/hooks/hook-specification)
* [Creating a project hook](http://sailsjs.com/documentation/concepts/extending-sails/Hooks/projecthooks.html)
* [Creating an installable hook](http://sailsjs.com/documentation/concepts/extending-sails/Hooks/installablehooks.html)



<docmeta name="displayName" value="Hooks">
<docmeta name="stabilityIndex" value="3">
