# hoodie-server-task

> CouchDB-based REST & front-end API for asynchronous background tasks

## Scope

The goal is to create very simplistic server for static apps that can
run background tasks that require back-end logic using a simple front-end
API.

## Install

```
npm install --save hoodie-server-task
```


## Server API

Example usage with [nodemailer](https://www.npmjs.com/package/nodemailer) to
send emails from the front-end

```js
var Hapi = require('hapi')
var hapiTask = require('hoodie-server-task')

var options = {
  couchdb: 'http://localhost:5984'
})

var transporter = nodemailer.createTransport({
  service: 'Gmail',
  auth: {
    user: 'mailer@example.com',
    pass: 'secret'
  }
})

server.register({register: hapiTask}, options, function (error) {
  var taskApi = server.plugins.task.api
  taskApi('mytask').on('start', function (task) {
    if (task.isFunky) {
      taskApi.success(task, function (error) {})
    } else {
      taskApi.success(task, 'you not funky!', function (error) {})
    }
  })
});

server.connection({
  port: 8000
});

server.start(function () {
  console.log('Server running at %s', server.info.uri);
});
```

## REST API

```
POST /api/user/<id>/_bulk_docs
GET /api/user/<id>/_changes
```

## How it works

Tasks are json objects with special properties. `hoodie-server-task` creates a
database (`tasks` by default) where all task objects from all users are
replicated to / from. Users can only access their own tasks
(`/api/user/<id>/_changes` is a filtered changes feed by the given user id).

## Local setup & tests

```bash
git clone git@github.com:hoodiehq/hoodie-server-task.git
cd hoodie-server-task
npm install
npm test
```

## License

MIT