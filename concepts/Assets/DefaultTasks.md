# Задачи по дефолту

### Обзор

Пайплайн активов встроенный в Sails это набор задач Grunt настроены с использованием стандартных настроек по умолчанию, призванных сделать ваш проект более последовательным и продуктивным. Весь рабочий процесс интерфейса frontend полностью настраивается, в то время как он предоставляет некоторые задачи по умолчанию из коробки. Sails упрощает это с помощью [конфигурирования новых задач](https://github.com/tvortsa/sails-docs/blob/1.0/concepts/Assets/TaskAutomation.md?q=task-configuration) под ваши нужды.
<!-- change link to: /documentation/concepts/assets/task-automation#?task-configuration once new site is live -->

Вот несколько вещей, которые конфигурация Grunt по умолчанию в Sails делает, чтобы помочь вам:
- Автоматическая компиляция LESS
- Автоматическая компиляция JST 
- Автоматическая компиляция Coffeescript 
- Опциональная автоматическая asset injection, minification, и concatenation
- Создание web ready public директории
- Просмотр и синхронизация файлов
- Optimization of assets in production

### Дефолтный задачи Grunt

Ниже приведен список задач Grunt которые включаются по умолчанию в новый Sails проект:

##### clean

> Эта grunt задача сконфигурирована на очистку содержимого в `.tmp/public/` вашего проекта sails.

> [usage docs](https://github.com/gruntjs/grunt-contrib-clean)

##### coffee

> Компилирует coffeeScript файлы из `assets/js/` в Javascript и размещает их в папке `.tmp/public/js/`.

> [usage docs](https://github.com/gruntjs/grunt-contrib-coffee)

##### concat

> Соединяет JavaScript и css файлы, и сохраняет соединенные файлы в папке `.tmp/public/concat/` .

> [usage docs](https://github.com/gruntjs/grunt-contrib-concat)

##### copy

> **dev task config**
> Копирует все файлы и папки, кроме coffeescript и less файлов, из папки активов sails в папку `.tmp/public/`.

> **build task config**
> Копирует все файлы и папки из папки .tmp/public в папку www.

> [usage docs](https://github.com/gruntjs/grunt-contrib-copy)

##### cssmin

> Минифицирует css файлы и помещает их в папку `.tmp/public/min/` .

> [usage docs](https://github.com/gruntjs/grunt-contrib-cssmin)

##### jst

> Прекомпиляция Underscore шаблонов в `.jst` файлы. (т.e. берет файлы HTML шаблонов и turns их в tiny JavaScript функции). Это ускоряет рендеринг шаблонов на клиенте, и сокращает использование полосы пропускания.

> [usage docs](https://github.com/gruntjs/grunt-contrib-jst)

##### less

> Компилирует файлы LESS в CSS. Компилируется только файл `assets/styles/importer.less` . Это позволяет вам самому контролировать порядок, импорт зависимостей, mixins, variables, resets, и т.п. прежде остальных таблиц стилей.

> [usage docs](https://github.com/gruntjs/grunt-contrib-less)

##### sails-linker

> Автоматически вставляет тэги `<script>` для JavaScript файлов и `<link>` тэги для css файлов.  Также автоматически линкует выходной файл, содержащий предварительно скомпилированные шаблоны используя `<script>` тэг. Более подробное описание этой задачи [здесь](https://github.com/balderdashy/sails-generate-frontend/blob/master/docs/overview.md#a-litte-bit-more-about-sails-linking), но большой взнос заключается в том, что сценарий и вставка таблицы стилей * выполняется * только в файлах, содержащих `<!--SCRIPTS--><!--SCRIPTS END-->` и/или `<!--STYLES--><!--STYLES END-->` тэги.  TОни включены по-умолчанию в файл **views/layout.ejs** нового Sails проекта.  Если вы не хотите использовать linker для вашего проекта, вы можете просто удалить эти теги.

> [usage docs](https://github.com/Zolmeister/grunt-sails-linker)

##### sync

> A grunt task to keep directories in sync. It is very similar to grunt-contrib-copy but tries to copy only those files that have actually changed. It specifically synchronizes files from the `assets/` folder to `.tmp/public/`, overwriting anything that's already there.

> [usage docs](https://github.com/tomusdrw/grunt-sync)

##### uglify

> Minifies client-side JavaScript assets.  Note that by default, this task will "mangle" all of your function and variable names (either by changing them to a much shorter name, or stripping them entirely).  This is usually desirable as it makes your code significantly smaller, but in some cases can lead to unexpected results (particularly when you expect an object's constructor to have a certain name).  To turn off or modify this behavior, [use the `mangle` option](https://github.com/gruntjs/grunt-contrib-uglify#no-mangling) when setting up this task.

> [usage docs](https://github.com/gruntjs/grunt-contrib-uglify)

##### watch

> Runs predefined tasks whenever watched file patterns are added, changed or deleted. Watches for changes on files in the `assets/` folder, and re-runs the appropriate tasks (e.g. less and jst compilation).  This allows you to see changes to your assets reflected in your app without having to restart the Sails server.

> [usage docs](https://github.com/gruntjs/grunt-contrib-watch)


<docmeta name="displayName" value="Default tasks">
