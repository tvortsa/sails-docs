# Расширение Sails

Согласно философии Node, Sails стремиться сохранять свое ядро минимальным, делегируя все, кроме самых критичных функций - отдельным модулям[*](./#foot1). На сегодня есть три типа расширений которые можно создавать для Sails:

+ [**Генераторы**](http://sailsjs.com/documentation/concepts/extending-sails/Generators) - для добавления и переопределения функциональности в Sails CLI.  *Example*: [sails-generate-model](https://www.npmjs.com/package/sails-generate-model), который позволяет создавать модели в командной строке с помощью `sails generate model foo`.
+ [**Адаптеры**](http://sailsjs.com/documentation/concepts/extending-sails/Adapters) - для интеграции Waterline (Sails' ORM) с новыми data sources, вклбчая databases, APIs, и даже железом. *Example*: [sails-postgresql](https://www.npmjs.com/package/sails-postgresql), официальный [PostgreSQL](http://www.postgresql.org/) адаптер для Sails.
+ [**Hooks**](http://sailsjs.com/documentation/concepts/extending-sails/Hooks) - для переопределения или ввода новых функциональных возможностей в Sails runtime.  *Example*: [sails-hook-autoreload](https://www.npmjs.com/package/sails-hook-autoreload), which adds auto-refreshing for a Sails project's API without having to manually restart the server.

If you&rsquo;re interested in developing a plugin for Sails, you will most often want to make a [hook](http://sailsjs.com/documentation/concepts/extending-sails/Hooks).

<sub><a name="foot1">*</a> _Core hooks_, как `http`, `request`, etc. это hooks встроенные в Sails из коробки.  They can be disabled by specifying a `hooks` configuration in your `.sailsrc` file, or when lifting Sails programatically.</sub>


<docmeta name="displayName" value="Extending Sails">
