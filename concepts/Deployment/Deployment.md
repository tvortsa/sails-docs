# Развертывание

### Перед тем как развернуть

До того как запустить web приложение, вы должны спросить себя:

+ Какой трафик ожидается?
+ Требуется ли вам контракт на выполнение любых гарантийных сроков, e.g. a Service Level Agreement (SLA)?
+ Какие пользовательские агенты будут  "hitting" вашу инфраструктуру?
  + desktop web browsers
  + mobile web browsers (and what form factors?  Tablet or handset?  Or both?)
  + embedded browsers from smart TVs or gaming consoles
  + Android/iOS/Windows Phone apps
  + PhoneGap/Electron apps
  + Developers (cURL, Postman, AJAX requests, WebSocket front-end apps)
  + Other devices (tvs, watches, toasters..?)
+ And what kinds of things will they be requesting? (e.g. HTML? JSON? XML?)
+ Will you be taking advantage of realtime features with Socket.io?
  + e.g. chat, realtime analytics, in-app notifications/messages
+ How are you tracking crashes and errors?
  + Are you using `sails.log()`? Or are you using a custom logger from NPM like [Winston](https://github.com/winstonjs/winston)?  Or even easier, sticking with built-in logging from `sails.log()` in combination with a hosted service like [Papertrail](https://papertrailapp.com/)?
+ Have you tried running in production mode locally in production mode (with the `NODE_ENV` environment variable set to "production")?
  + A quick way to test this out is to run `NODE_ENV=production node app` (or, as a shortcut: `sails lift --prod`).


### Настройка приложения для production

Вы можете предоставить конфигурацию, которая применяется только в production [несколькими разными способами](http://sailsjs.com/documentation/reference/configuration).  Большинство приложений обнаруживают, что используют сочетание переменных среды и `config/env/production.js`.  Но независимо от того, как вы это делаете, Этот раздел и [Scaling section](http://sailsjs.com/documentation/concepts/deployment/scaling) окументации описывают параметры конфигурации, которые вы должны просмотреть и / или изменить, прежде чем перейти к production.



### Развертывание на единичном сервере

Node.js довольно быстр.  Для большинства приложений, Одного сервера достаточно для обработки ожидаемого трафика - по крайней мере, сначала.

> Этот раздел посвящен _single-server Sails deployment_.  Такое развертывание по своей сути ограничено по масштабам.  См [Scaling](http://sailsjs.com/documentation/concepts/deployment/scaling) для получения информации о развертывании приложения Sails/Node за балансировщиком нагрузки.

Многие команды решают развернуть свое производственное приложение за балансировщиком нагрузки или прокси-сервером (e.g. in a PaaS like Heroku or Modulus, or behind an nginx server).  Это часто правильный подход, поскольку он помогает будущему вашему приложению в случае необходимости изменения масштабируемости, и вам нужно добавить больше серверов.  Если вы используете балансировщик нагрузки или прокси-сервер, в приведенном ниже списке есть несколько вещей, которые вы можете игнорировать:

+ Не беспокойтесь о настройке Sails на использование SSL сертификата.  SSL почти всегда будут разрешены на вашем балансировщике нагрузки / прокси-сервере или провайдере PaaS.
+ you _probably_ не нужно беспокоиться о том, как настроить приложение на порт 80 (если не за прокси, как nginx). Большинство провайдеров PaaS автоматически определяют порт для вас.  If you are using a proxy server, please refer to its documentation (whether or not you need to configure the port for your Sails app depends on how you set things up and can vary widely based on your needs).

> If your app uses sockets and you're using nginx, be sure to configure it to relay websocket messages to your server. You can find guidance on proxying WebSockets in [nginx's docs on the subject](http://nginx.org/en/docs/http/websocket.html).


##### Установка `NODE_ENV` переменной окружения в `'production'`

Настройка конфигурации приложения в  `'production'` говорит Sails to get its game face on; i.e. что ваше приложение запущено в  production окружении.  Это, hands down, самый важный шаг. Если у вас есть время только на одну настройку - то это она!

Когда ваше приложение запускается в production окружении:
  + Middleware и другие зависимости заключенные в Sails переходят на использование более эффективного кода.
  + Все ваши [models' migration settings](http://sailsjs.com/documentation/concepts/models-and-orm/model-settings) вынуждены `migrate: 'safe'`.  Это безопасное средство защиты от непреднамеренного повреждения производственных данных во время развертывания.
  + Ваш asset pipeline запускается в production mode (если это необходимо).  Из коробки, это означает что Sails app будет компилить все stylesheets, client-side scripts, и precompiled JST templates в minified `.css` и `.js` files to decrease page load times and reduce bandwidth consumption.
  + Error messages и stack traces из `res.serverError()` будут продолжать логироваться, но не пересылаться в отзывах (this is to prevent a would-be attacker from accessing any sensitive information, such as encrypted passwords or the path where your Sails app is located on the server's file system)


>**Note:**
>Если вы задаете [`sails.config.environment`](http://sailsjs.com/documentation/reference/configuration/sails-config#?sailsconfigenvironment) в `'production'` каким-то другим способом, это хорошо.  Но заметьте что Sails либо установит `NODE_ENV` переменную в `'production'` для вас автоматически (или иначе запишет предупреждение - так что следите за консолью!).  Причина, по которой эта переменная среды настолько важна, заключается в том, что она является универсальной конвенцией в Node.js, независимо от используемой структуры.  Встроенное middleware и зависимости в Sails _expect_ `NODE_ENV` чтобы быть установленными в production-- в противном случае они используют свои менее эффективные кодовые пути, предназначенные только для разработки.

##### Set a `sails.config.sockets.onlyAllowOrigins` value

If you have sockets enabled for your app (that is, you have the `sails-hook-sockets` module installed), then for security reasons you'll need to set `sails.config.sockets.onlyAllowOrigins` to the array of origins that should be allowed to connect to your app via websockets.  You&rsuqo;ll likely set this in your app&rsquo;s `config/env/production.js` file.  See the [socket configuration documentation](http://sailsjs.com/documentation/reference/configuration/sails-config-sockets) for more info on `onlyAllowOrigins`.


##### Configure your app to run on port 80

Whether it's by using the `sails_port` environment variable, setting the `--port` command-line option, or changing your production config file(s), add the following to the top level of your Sails config:

```javascript
port: 80
```

> As mentioned above, ignore this step if your app will be running behind a load balancer or proxy.



##### Настройка production БД для ваших моделей

Если все модели вашего приложения используют дефолтную БД (default datastore), то настройка вашей продакшн БД просто, как настройка `sails.config.datastores.default` в файле [config/env/production.js](http://sailsjs.com/documentation/concepts/configuration#?environmentspecific-files-config-env) корректными параметрами.

If your app is using more than one database, your process will be similar.  For every datastore used by the app, add an item to the `sails.config.datastores` dictionary in [config/env/production.js](http://sailsjs.com/documentation/concepts/configuration#?environmentspecific-files-config-env).

Keep in mind that if you are using version control (e.g. git), then any sensitive credentials (such as database passwords) will be checked in to the repo if you include them in your app's configuration files.  A common solution to this problem is to provide certain sensitive configuration settings as environment variables.  See [Configuration](http://sailsjs.com/documentation/concepts/configuration) for more information.

If you are using a relational database such as MySQL, there is an additional step.  Remember how Sails sets all your models to `migrate:safe` when run in production? Это означает, что автоматические миграции не выполняются при запуске приложения...Что означает, что по умолчанию ваши таблицы не будут существовать.  A common approach to deal with this during the first-time setup of a relational database for your Sails app goes as follows:
  + Create the database on the production database server (e.g. `frenchfryparty`)
  + Configure your app locally to use this production database, but _don't set the environment to `'production'`, and leave your models' configuration set to `migrate: 'alter'`_.  Now run `sails lift` **once**-- and when the local server finishes lifting, kill it.
    + **Be careful!**  Вы должны делать это только тогда, когда _данных нет_ в production database.

Если это вас раздражает, или если вы не можете подключиться к базе данных производства удаленно, вы можете пропустить описанные выше шаги.  Вместо этого просто удалите локальную схему и импортируйте ее в производственную базу данных.


##### Enable CSRF protection

Protecting against CSRF is an important security measure for Sails apps.  If you haven't already been developing with CSRF protection enabled (see [`sails.config.security.csrf`](http://sailsjs.com/documentation/reference/configuration/sails-config-security-csrf)), be sure to [enable CSRF protection](http://sailsjs.com/documentation/concepts/Security/CSRF.html?q=enabling-csrf-protection) before going to production.



##### Enable SSL

If your API or website does anything that requires authentication, you should use SSL in production.  To configure your Sails app to use an SSL certificate, use [`sails.config.ssl`](http://sailsjs.com/documentation/reference/configuration/sails-config).

> As mentioned above, ignore this step if your app will be running behind a load balancer or proxy (e.g. on a PaaS like Heroku).



##### Поднимаем приложение

Последний шаг развертывания - это фактически запуск сервера.  Например:

```bash
NODE_ENV=production node app.js
```

Или, если вам удобнее использовать параметры командной строки, вы можете использовать `--prod`:

```bash
node app.js --prod
# (Sails will set `NODE_ENV` automatically)
```

Как вы можете видеть, вместо `sails lift` вы должны запускать ваше приложение Sails командой `node app.js` в production.  Таким образом, ваше приложение не полагается на доступ к `sails` command-line tool; it just runs the `app.js` file bundled in your Sails app (который делает то же самое).


##### ...И держите его поднятым

Если вы не собираетесь использовать PaaS, например Heroku, вы захотите использовать такой инструмент, как [`pm2`](http://pm2.keymetrics.io/) или [`forever`](https://github.com/foreverjs/forever) Чтобы убедиться, что ваш сервер приложений начнет резервное копирование, если он крашнется.  Независимо от выбранного вами демона, вы должны убедиться, что он запускает сервер, как описано выше.



### Следующие щаги

+ [Scaling your Sails/Node.js app](http://sailsjs.com/documentation/concepts/deployment/scaling)
+


<docmeta name="displayName" value="Deployment">
