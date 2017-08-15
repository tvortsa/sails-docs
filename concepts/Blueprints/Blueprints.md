# Blueprints

### Обзор

Как любой хороший web фрэймворк, Sails нацелен на сокращение как количества кода, который вы пишете, так и времени, необходимого для запуска и запуска функционального приложения.  _Blueprints_ это Sails&rsquo;s способ быстрой генерации API [routes](http://sailsjs.com/documentation/concepts/routes) и [actions](http://sailsjs.com/documentation/concepts/controllers#?actions) основанном на дизайне вашего приложения.

Вместе, [blueprint routes](http://sailsjs.com/documentation/concepts/blueprints/blueprint-routes) и [blueprint actions](http://sailsjs.com/documentation/concepts/blueprints/blueprint-actions) составляют **blueprint API**, встроенная логика на основе [RESTful JSON API](http://en.wikipedia.org/wiki/Representational_state_transfer) которую вы получаете всякий раз создавая model и controller.

НАпример, если вы создали файл модели `User.js` в своем проекте, то с включенным blueprints вы сможете сразу посетить `/user/create?name=joe` для создания user, и посетить `/user` чтобы увидеть массив users вашего приложения. 

Blueprints является мощным инструментом для прототипирования, но во многих случаях может быть использован и в производстве, поскольку он может быть переопределен, защищен, расширен или полностью отключен.

### Далее

+ [Read more](http://sailsjs.com/documentation/concepts/blueprints/blueprint-actions) о встроенных blueprint actions
+ [Read more](http://sailsjs.com/documentation/concepts/blueprints/blueprint-routes) о неявных "shadow" routes и как настроить или переопределить их

<docmeta name="displayName" value="Blueprints">
