# api/controllers/

Это папка которая содержит ваши контроллеры.  В Sails, контроллеры это JavaScript файлы которые содержат логику взаимодействия с моделями и рендеринга соответствующих views на клиенте.

Когда вы вызываете `sails generate api cats` через командную строку в корневой папке вашего приложения, Sails генерирует файл `api/controllers/CatsController.js` а также соответствующую модель.

Папка `api/controllers` может также содержать _standalone actions_, которые являются JavaScript файлами содержащими _single_ controller action, rather than a dictionary of actions.

See the [main actions and controllers documentation](http://sailsjs.com/documentation/concepts/actions-and-controllers) for more info.

<docmeta name="displayName" value="controllers">


