# tasks/pipeline.js

Файл `pipeline.js` вашего Sails приложения определяет порядок в котором stylesheets,
JavaScript, и client-side template должны компилироваться и линковаться в теги `<script>`
или `<link>` .

If you are not relying on [automatic asset linking](http://sailsjs.com/documentation/concepts/assets/task-automation#?asset-pipeline), then you can safely ignore this file.

> Note that you can take advantage of Grunt-style wildcard/glob/splat expressions for matching multiple files, and use `!` in front of an expression to ignore files.


<docmeta name="displayName" value="pipeline.js">
