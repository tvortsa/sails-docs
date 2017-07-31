# Развертывание

### Перед развертыванием

Перед запуском любого веб-приложения вы должны задать себе несколько вопросов:

+ Каков ваш ожидаемый трафик?
+ Are you contractually required to meet any uptime guarantees, e.g. a Service Level Agreement (SLA)?
+ What sorts of front-end apps will be "hitting" your infrastructure?
  + Android/iOS/Windows Phone apps
  + desktop web browsers
  + mobile web browsers (tablets, phones, iPad minis?)
  + Cordova/Electron apps
  + tvs, watches, toasters..?
+ And what kinds of things will they be requesting? (e.g. HTML? JSON? XML?)
+ Will you be taking advantage of realtime pubsub features with Socket.io?
  + e.g. chat, realtime analytics, in-app notifications/messages
+ How are you tracking crashes and errors?
  + Are you using `sails.log()`? Or are you using a custom logger from NPM like [Winston](https://github.com/winstonjs/winston)?



### Настройка приложения для Production

Вы можете предоставить конфигурацию, которая применяется только в [несколькими разными путями](http://sailsjs.com/documentation/reference/configuration).  Большинство приложений обнаруживают, что используют сочетание переменных среды и `config/env/production.js`.  Но независимо от того, как вы это делаете, Этот раздел и [Scaling раздел](http://sailsjs.com/documentation/concepts/deployment/scaling) документации раскрывает араметры конфигурации, которые вы должны просмотреть и / или изменить перед тем, как перейти к production.



### Развертывание на одном сервере

Node.js весьма быстрый.  Для большинства приложений, одного сервера достаточно для обработки ожидаемого трафика-- по крайней мере, сначала.

> Это раздел фокусируется на _single-server Sails deployment_.  Такое развертывание по своей сути ограничено по масштабам.  См. [Scaling](http://sailsjs.com/documentation/concepts/deployment/scaling) for information about deploying your Sails/Node app behind a load balancer.

Many teams decide to deploy their production app behind a load balancer or proxy (e.g. in a PaaS like Heroku or Modulus, or behind an nginx server).  This is often the right approach since it helps future-proof your app in case your scalability needs change and you need to add more servers.  If you are using a load balancer or proxy, there are a few things in the list below that you can ignore:

+ не беспокойтесь о настройке Sails дляиспользования SSL сертификата.  SSL почти всегда решается вашим load balancer/proxy сервером, или вашим PaaS провайдером.
+ you _probably_ don't need to worry about setting your app to run on port 80 (if not behind a proxy like nginx). Most PaaS providers automatically figure out the port for you.  If you are using a proxy server, please refer to its documentation (whether or not you need to configure the port for your Sails app depends on how you set things up and can vary widely based on your needs).

> If your app uses sockets and you're using nginx, be sure to configure it to relay websocket messages to your server. You can find guidance on proxying WebSockets in [nginx's docs on the subject](http://nginx.org/en/docs/http/websocket.html).


##### Настройка `NODE_ENV` переменной окружения на `'production'`

Установка конфигурации приложения в приложении в `'production'` говорит Sails to get its game face on; т.e. ваше приложение работает в production environment.  Это наиболее важный шаг. Если у вас есть время, чтобы изменить только _jlye yfcnhjqre_ перед развертыванием вашего приложения Sails, _то это должна быть эта настройка_!

Когда ваше приложение запущено в окружении production:
  + Middleware и другие зависимости dependencies запеченные в Sails переключаются на более эффективный код.
  + Все ваши [models' migration settings](http://sailsjs.com/documentation/concepts/models-and-orm/model-settings) переводятся в режим  `migrate: 'safe'`.  Это безопасное средство защиты от непреднамеренного повреждения производственных данных во время развертывания.
  + Ваш asset pipeline запускается в production режиме (if relevant).  Из коробки это значит что Sails приложение скомпилирует все  stylesheets, client-side scripts, и precompiled JST templates в minified `.css` и `.js` файлы.
  + Error messages и stack traces из `res.serverError()` продолжат логгироваться, но не будут направлены в ответ (Это предотвращает доступ потенциального злоумышленника к любой конфиденциальной информации, такой как encrypted passwords или path where your Sails app is located on the server's file system)


>**Note:**
>If you set [`sails.config.environment`](http://sailsjs.com/documentation/reference/configuration/sails-config#?sailsconfigenvironment) to `'production'` some other way, that's totally cool.  Just note that Sails will set the `NODE_ENV` environment variable to `'production'` for you automatically.  The reason this environment variable is so important is that it is a universal convention in Node.js, regardless of the framework you are using.  Built-in middleware and dependencies in Sails _expect_ `NODE_ENV` to be set in production-- otherwise they use their less efficient code paths that were designed for development use only.




##### Настройте приложение для работы на порту 80

Whether it's by using the `sails_port` environment variable, setting the `--port` command-line option, or changing your production config file(s), add the following to the top level of your Sails config:

```javascript
port: 80
```

> As mentioned above, ignore this step if your app will be running behind a load balancer or proxy.



##### Set up production database(s) for your models

To set up one or more production databases, configure them in [`sails.config.connections`](http://sailsjs.com/documentation/reference/configuration/sails-config-connections), and then refer to them from [`sails.config.models.connection`](http://sailsjs.com/documentation/reference/configuration/sails-config-models) and/or from individual models.  For most apps, your production config changes are pretty simple:
1. add a connection representing your production database (e.g. `productionPostgresql: { ... }`)
2. override the default connection (`sails.config.models.connection`) to point to your production database (e.g. `productionPostgresql`)

If your app is using more than one database, your process will be similar.  However, you will probably find that it's easier to override existing connection settings rather than adding new connections and changing individual models to point at them. Regardless how you go about it, if you are using multiple databases you should be sure your models are pointed at the right connections when you deploy to production.

Keep in mind that if you are using version control (e.g. git), then any sensitive credentials (such as database passwords) will be checked in to the repo if you include them in your app's configuration files.  A common solution to this problem is to provide certain sensitive configuration settings as environment variables.  See [Configuration](http://sailsjs.com/documentation/concepts/configuration) for more information.

If you are using a relational database such as MySQL, there is an additional step.  Remember how Sails sets all your models to `migrate:safe` when run in production?  That means no auto-migrations are run when lifting the app...which means by default your tables won't exist.  A common approach to deal with this during the first-time setup of a relational database for your Sails app goes as follows:
  + Create the database on the production database server (e.g. `frenchfryparty`)
  + Configure your app locally to use this production database, but _don't set the environment to `'production'`, and leave your models' configuration set to `migrate: 'alter'`_.  Now run `sails lift` **once**-- and when the local server finishes lifting, kill it.
    + **Be careful!**  You should only do this when there is _no data_ in the production database.

If this makes you nervous or if you can't connect to the production database remotely, you can skip the steps above.  Instead, simply dump your local schema and import it into the production database.


##### Enable CSRF protection

Protecting against CSRF is an important security measure for Sails apps.  If you haven't already been developing with CSRF protection enabled (see [`sails.config.csrf`](http://sailsjs.com/documentation/reference/configuration/sails-config-csrf)), be sure to [enable CSRF protection](http://sailsjs.com/documentation/concepts/Security/CSRF.html?q=enabling-csrf-protection) before going to production.



##### Enable SSL

If your API or website does anything that requires authentication, you should use SSL in production.  To configure your Sails app to use an SSL certificate, use [`sails.config.ssl`](http://sailsjs.com/documentation/reference/configuration/sails-config).

> As mentioned above, ignore this step if your app will be running behind a load balancer or proxy.



##### Lift Your App

Последний шаг в развертывании - запуск вашего сервера.  Нарример:

```bash
NODE_ENV=production node app.js
```

Или, если вам удобнее использовать параметры командной строки, вы можете использовать `--prod`:

```
node app.js --prod
# (Sails установит `NODE_ENV` автоматически)
```

Как видите, вместо `sails lift` вы долджны запускать приложение Sails app командой `node app.js` в продакшне.  Таким образом, ваше приложение не полагается на доступ к инструменту командной строки `sails`; it just runs the `app.js` file bundled in your Sails app (which does exactly the same thing).


##### ...And Keep It Lifted

Если вы не развертываете на PaaS типа Heroku или Modulus,Вы захотите использовать такой инструмент, как [`pm2`](http://pm2.keymetrics.io/) или [`forever`](https://github.com/foreverjs/forever) Чтобы убедиться, что ваш сервер приложений начнет резервное копирование, если он крашнется.  Независимо от выбранного вами демона, вы должны убедиться, что он запускает сервер, как описано выше.

For convenience, here are example lift commands for both `pm2` and `forever`:

Using `pm2`:

```bash
pm2 start app.js -x -- --prod
```

Using `forever`:

```bash
forever start app.js --prod
```




<docmeta name="displayName" value="Deployment">
