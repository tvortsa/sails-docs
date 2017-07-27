# Gruntfile.js

### Назначение

Sails использует [Grunt](http://gruntjs.com) для управления активами. Этот файл содержит точку входа для дефолтного пайплайна активов в Sails; тоесть, код который делает такие вещи как компиляцию LESS стилей, минификацию скриптов для продакшна, и прекомпиляцию и инъекцию client-side шаблонов.

Интеграция Sails с Grunt полностью настраиваема, но для большинства ситуаций, этот файл (`Gruntfile.js`) должен оставаться неизменным.  Лучше, устанавливать Grunt плагины или добавлять вашу кастомную логику в новые файлы в папку [`tasks/`](./tasks).

> + Узнать больше о работе со статическими активами в Sails, можно тут [conceptual documentation on assets](http://sailsjs.com/documentation/concepts/assets).
> + Более обширное введение в Grunt tasks in general, тут [Grunt's docs on configuring tasks](http://gruntjs.com/configuring-tasks).


<docmeta name="displayName" value="Gruntfile.js">
