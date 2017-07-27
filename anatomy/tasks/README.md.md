# README.md
### Назначение
Если вы хотите создать [README file](http://en.wikipedia.org/wiki/README) для вашего приложения, то это хорошее место для него.  Если вы хостите свое приложение на Github, содержимое этого файла будет отображаться внизу на странице Github реппозитория.



<docmeta name="displayName" value="README.md">

```
# О папке `tasks`

Папка `tasks` представляет собой набор задач Grunt и их конфигураций, собранная для вашего удобства.  Интеграция Grunt в основном полезна для сборки front-end активов, (таблицы стилей, скрипты, & шаблоны разметки) но он также может использоваться для выполнения всех видов задач разработки, from browserify compilation до database migrations.

Если вы не использовали [Grunt](http://gruntjs.com/) ранее, загляните в гайд [Getting Started](http://gruntjs.com/getting-started) и узнайте о том как создавать задачи [Gruntfile](http://gruntjs.com/sample-gruntfile) и как устанавливать и использовать плагинв Grunt. Once you're familiar with that process, read on!


### Как это работает?

Пайплайн сборки активов в Sails это набор задач (tasks) Grunt сконфигурированные с использованием стандартных значений по умолчанию, предназначенных для того, чтобы сделать ваш проект более последовательным и продуктивным.

Весь рабочий процесс активов front-end в Sails полностью настраиваем-- в то же время как он предлагает некоторые предложения из коробки, Sails не претендует на то, что он может предвидеть все потребности, с которыми вам придется столкнуться собирая browser-based/front-end portion of your application.  Кто скажет, что вы даже создаете приложение для браузера?



### Какие задачи  Sails запускает автоматически?

Sails запускает некоторые из этих задач (те которые в папке `tasks/register`) автоматически когда вы запускаете одну из соответствующих команд.

###### `sails lift`

Запускает задачу `default` task (`tasks/register/default.js`).

###### `sails lift --prod`

Запускает задачу `prod`  (`tasks/register/prod.js`).

###### `sails www`

Запускает задачу `build`  (`tasks/register/build.js`).

###### `sails www --prod` (production)

Запускает задачу `buildProd`  (`tasks/register/buildProd.js`).


### Могу я кастомизировать это для SASS, Angular, client-side Jade templates, и т.п.?

Вы можете изменять, опускать или заменять любую из этих задач Grunt в соответствии с вашими требованиями. Вы также можете добавить свои собственные задачи Grunt - просто добавьте файл `someTask.js` в каталог` grunt / config`, чтобы настроить новую задачу, Затем зарегистрируйте его с соответствующей родительской задачей(задачами) (см. файлыsee в `grunt/register/*.js`).


### Должен ли я использовать Grunt?

Нет! Чтобы отключить интеграицию с Grunt в Sails, просто удалите ваш Gruntfile или disable Grunt hook.


### Что делать, если я не создаю frontend?

Все ок! Основной принцип Sails клиент-агностицизм-- он специально предназначен для строительства APIs используемого всеми видами клиентов; native Android/iOS/Cordova, serverside SDKs, etc.

Вы можете полностью отключить Grunt следуя инструкциям выше.

If you still want to use Grunt for other purposes, but don't want any of the default web front-end stuff, just delete your project's `assets` folder and remove the front-end oriented tasks from the `grunt/register` and `grunt/config` folders.  You can also run `sails new myCoolApi --no-frontend` to omit the `assets` folder and front-end-oriented Grunt tasks for future projects.  You can also replace your `sails-generate-frontend` module with alternative community generators, or create your own.  This allows `sails new` to create the boilerplate for native iOS apps, Android apps, Cordova apps, SteroidsJS apps, etc.


```
