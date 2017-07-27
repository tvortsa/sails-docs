# api/

Эта папка содержит основной функционал бэк-энд логики вашего приложения.  Это дом для 'M' и 'C' в <a href="http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller" target="_blank">MVC Framework</a>.

Тут вы найдете:

- Controllers: контроллеры содержашие большую часть бэк-энд логики вашего приложения.
- Helpers: Хэлперы это общие утилиты которые могут быть вызваны из любого места в приложении.
- Models: Модели это структуры содержащие данные вашего приложения Sails.
- Policies: Политики обычно используют для аутентификации пользователей и ограничения доступа к некоторым частям вашего приложения.

Вы также можете найти эти папки, которые не генерируются по-умолчанию в новом приложении Sails :

- Responses: Логика ответа сервера (see the [custom responses docs](http://sailsjs.com/documentation/concepts/extending-sails/custom-responses) for more info).
- Services: Services are shared utilities typically created in Sails apps before version 1.0.  In Sails 1.0 apps, рекомендуется вместо них использовать _helpers_ .



<docmeta name="displayName" value="api">

