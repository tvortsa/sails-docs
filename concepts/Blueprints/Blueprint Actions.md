# Blueprint actions

Blueprint actions (не путать с неявным [blueprint "action" _routes_](http://sailsjs.com/documentation/concepts/blueprints/blueprint-routes#?action-routes)) это generic actions для работы с вашими моделями.  Думайте о них как о поведении по умолчанию для вашего приложения.  Например если у вас есть модель `User.js`то `find`, `create`, `update`, `destroy`, `populate`, `add` и `remove` actions существуют неявно, без необходимости написания их вами.

По умолчанию, blueprint [RESTful routes](http://sailsjs.com/documentation/concepts/blueprints/blueprint-routes#?restful-routes) и [shortcut routes](http://sailsjs.com/documentation/concepts/blueprints/blueprint-routes#?shortcut-routes) связаны с соответствующими им blueprint actions.  Однако, любое blueprint action может быть overridden ля конкретного контроллера путем создания пользовательского action в этом файле контроллера (e.g. `ParrotController.find`).

Текущая версия Sails поставляется со следующими blueprint actions:

+ [find](http://sailsjs.com/documentation/reference/blueprint-api/find)
+ [findOne](http://sailsjs.com/documentation/reference/blueprint-api/findOne)
+ [create](http://sailsjs.com/documentation/reference/blueprint-api/create)
+ [update](http://sailsjs.com/documentation/reference/blueprint-api/update)
+ [destroy](http://sailsjs.com/documentation/reference/blueprint-api/destroy)
+ [populate](http://sailsjs.com/documentation/reference/blueprint-api/populate)
+ [add](http://sailsjs.com/documentation/reference/blueprint-api/add)
+ [remove](http://sailsjs.com/documentation/reference/blueprint-api/remove)
+ [replace](http://sailsjs.com/documentation/reference/blueprint-api/replace)

### Socket notifications

Большинство blueprint actions имеют функции реального времени, которые вступают в силу, если ваше приложение имеет активированные WebSockets.  Например, если **find** blueprint action принимает запрос от socket клиента, оно будетit [subscribe](http://sailsjs.com/documentation/reference/web-sockets/resourceful-pub-sub/subscribe) этот socket на следующие уведомления.  Затем любые time records измененные с использованием таких действий, как **update**, Sails будет [publish](http://sailsjs.com/documentation/reference/web-sockets/resourceful-pub-sub/publish) соответствующие уведомления.

Лучший способ понять поведение конкретного blueprint action прочесть его [страницу руководства](http://sailsjs.com/documentation/reference/blueprint-api) (или см. список выше).  Но если вы ищете больше общий взгляд на то как  работают функции реального времени Sails's blueprint API, см [**Concepts > Realtime**](http://sailsjs.com/documentation/concepts/realtime).  (Если вы не против некоторых устаревших деталей, можете посмотреть [original "Intro to Sails.js" video from 2013](https://www.youtube.com/watch?v=GK-tFvpIR7c).)

> Для более подробной разбивки всех уведомлений, опубликованных в Sails, см:
> + [Chart A (scenarios vs. notification types)](https://docs.google.com/spreadsheets/d/10FV9plyHR4gE9xIomIZlF-YS1S54oHEdvH8ZmTC1Fnc/edit#gid=0)
> + [Chart B (actions vs. recipients)](https://docs.google.com/spreadsheets/d/1B6i8aOoLNLtxJ4aeiA8GQ2lUQSvLOrP89RSLr7IAImw/edit#gid=0)

### Overriding blueprint actions

You may also override any of the blueprint actions for a controller by defining a [custom action](http://sailsjs.com/documentation/concepts/actions-and-controllers) with the same name.

```javascript
// api/controllers/user/UserController.js
module.exports = {

  /**
   * A custom action that overrides the built-in "findOne" blueprint action.
   * As a dummy example of customization, imagine we were working on something in our app
   * that demanded we tweak the format of the response data, and that we only populate two
   * associations: "company" and "friends".
   */
  findOne: function (req, res) {

    sails.log.debug('Running custom `findOne` action.  (Will look up user #'+req.param(\'id\')...');

    User.findOne({ id: req.param('id') }).omit(['password'])
    .populate('company', { select: ['profileImageUrl'] })
    .populate('top8', { omit: ['password'] })
    .exec(function(err, userRecord) {
      if (err) {
        switch (err.name) {
          case 'UsageError': return res.badRequest(err);
          default: return res.serverError(err);
        }
      }

      if (!userRecord) { return res.notFound(); }

      if (req.isSocket) {
        User.subscribe(req, [user.id]);
      }

      return res.ok({
        model: 'user',
        luckyCoolNumber: Math.ceil(10*Math.random()),
        record: userRecord
      });
    });
  }

}
```

> Alternatively, we could have created this as a standalone action at `api/controllers/user/find-one.js` or used [actions2](http://sailsjs.com/documentation/concepts/actions-and-controllers#?actions-2).

<docmeta name="displayName" value="Blueprint actions">
