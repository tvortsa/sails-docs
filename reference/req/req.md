# Request (`req`)

Sails построен поверх [Express](https://github.com/balderdashy/sails-docs/blob/master/PAGE_NEEDED.md), и использует [Node's HTTP server](http://nodejs.org/api/http.html) соглашения.  Поэтому, вам доступны все Node и Express методы и свойства в объекте `req` везде где он доступен (т.e. в controllers, policies, и custom responses.)

Хорошим побочным эффектом этой совместимости является то, что во многих случаях вы можете вставить существующий код Node.js в приложение Sails, и он будет работать.  И поскольку Sails реализует транспортно-агностический запрос-интерпретатор, код в вашем приложении Sails также совместим с WebSocket.

Sails добавляет несколько методов и собственных свойств к объекту `req`, like [`req.wantsJSON`](http://sailsjs.com/documentation/reference/req/req.wantsJSON.html) and [`req.allParams()`](http://sailsjs.com/documentation/reference/req/req.allParams.html).  Эти функции представляют собой синтаксический сахар поверх базовой реализации, а также поддерживают как HTTP, так и WebSockets.

### Protocol Support

В приведенной ниже таблице описывается поддержка методов и свойств объекта Sails [Request](http://sailsjs.com/documentation/reference/req)  (`req`) across multiple transports:

<!-- TODO: add SPDY -->


|    | HTTP    | WebSockets |
|----|---------|------------|
| req.file() | :white_check_mark: | :white_large_square: |
| req.param() | :white_check_mark: | :white_check_mark: |
| req.route | :white_check_mark: | :white_check_mark: |
| req.cookies | :white_check_mark: | :white_large_square: |
| req.signedCookies | :white_check_mark: | :white_large_square: |
| req.get() | :white_check_mark: | :white_large_square: |
| req.accepts() | :white_check_mark: | :white_large_square: |
| req.accepted | :white_check_mark: | :white_large_square: |
| req.is() | :white_check_mark: | :white_large_square: |
| req.ip | :white_check_mark: | :white_check_mark: |
| req.ips | :white_check_mark: | :white_large_square: |
| req.path | :white_check_mark: | :white_large_square: |
| req.host | :white_check_mark: | :white_large_square: |
| req.fresh | :white_check_mark: | :white_large_square: |
| req.stale | :white_check_mark: | :white_large_square: |
| req.xhr | :white_check_mark: | :white_large_square: |
| req.protocol | :white_check_mark: | :white_check_mark: |
| req.secure | :white_check_mark: | :white_large_square: |
| req.session | :white_check_mark: | :white_check_mark: |
| req.subdomains | :white_check_mark: | :white_large_square: |
| req.method | :white_check_mark: | :white_check_mark: |
| req.originalUrl | :white_check_mark: | :white_large_square: |
| req.acceptedLanguages | :white_check_mark: | :white_large_square: |
| req.acceptedCharsets | :white_check_mark: | :white_large_square: |
| req.acceptsCharset() | :white_check_mark: | :white_large_square: |
| req.acceptsLanguage() | :white_check_mark: | :white_large_square: |
| req.isSocket | :white_check_mark: | :white_check_mark: |
| req.params.all() | :white_check_mark: | :white_check_mark: |
| req.socket.id | :heavy_multiplication_x: | :white_check_mark: |
| req.socket.join | :heavy_multiplication_x: | :white_check_mark: |
| req.socket.leave | :heavy_multiplication_x: | :white_check_mark: |
| req.socket.broadcast  | :heavy_multiplication_x: | :white_check_mark: |
| req.transport  | :white_large_square: | :white_check_mark: |
| req.url | :white_check_mark: | :white_check_mark: |
| req.wantsJSON | :white_check_mark: | :white_check_mark: |


### Legend

  - :white_check_mark: - fully supported
  - :white_large_square: - feature not yet implemented
  - :heavy_multiplication_x: - unsupported due to protocol restrictions



<docmeta name="displayName" value="Request (`req`)">
<docmeta name="stabilityIndex" value="3">
