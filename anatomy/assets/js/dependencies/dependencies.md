# assets/js/dependencies
### НАзначение
Эта папка позволяет grunt загружать javascript файлы в index.ejs до того как загрузятся остальные javascript файлы в папке js.

    js/
    | main.js
    | apple.js
    | dependencies/
    | | sails.io.js

Такой сетап создаст:

    <!--SCRIPTS-->
    <script src="/js/dependencies/sails.io.js"></script>
    <script src="/js/apple.js"></script>
    <script src="/js/main.js"></script>
    <!--SCRIPTS END-->



<docmeta name="displayName" value="dependencies">

