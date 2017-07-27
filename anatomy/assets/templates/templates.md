# assets/templates/

Client-side HTML Шаблоны являются важными предпосылками Для определенных типов современных, богатых клиентских приложений, созданных для браузеров; в частности [SPAs](https://en.wikipedia.org/wiki/Single-page_application). Для того чтобы их магия работала, фрэймворки типа Backbone, Angular, Ember, и Knockout требуют, чтобы вы загружали шаблоны на стороне клиента; полностью отделен от ваших традиционных [server-side views](http://sailsjs.com/documentation/concepts/views).  Out of the box, new Sails apps поддерживает лучшее из обоих миров.

Независимо от того, используете ли вы шаблоны на стороне клиента в своем приложении и где вы их размещаете, конечно, полностью зависит от вас.  Но для соглашения новые приложения, созданные с помощью Sails, включают по умолчанию папку `templates /`.


### Как использовать эти templates?

По умолчанию, ваш Gruntfile is configured to automatically load and precompile
client-side JST templates in your `assets/templates` folder, then
include them in your `layout.ejs` view automatically (between TEMPLATES and TEMPLATES END).

    <!--TEMPLATES-->

    <!--TEMPLATES END-->

This exposes your HTML templates as precompiled functions on `window.JST` for use from your client-side JavaScript.

To customize this behavior to fit your needs, just edit your Gruntfile.
For example, here are a few things you could do:

- Import templates from other directories
- Use a different template engine (handlebars, jade, dust, etc)
- Internationalize your client-side templates using a server-side stringfile before they're served.


For more information, check out the conceptual documentation on the [default Grunt tasks](http://sailsjs.com/documentation/concepts/assets/default-tasks) that make up Sails' asset pipeline.

<docmeta name="displayName" value="templates">

