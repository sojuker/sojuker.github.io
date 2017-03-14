## 1、basic

```javascript
var express = require('../../lib/express');
var app = express();

// fake a database
var users = [
    { id: 0, name: 'tj', email: 'tj@vision-media.ca', role: 'member' }
  , { id: 1, name: 'ciaran', email: 'ciaranj@gmail.com', role: 'member' }
];

// middleware
function loadUser(req, res, next) {
    // app.get("/xxx/:id") ==> req.params.id
    var user = users[req.params.id];
    if (user) {
        req.user = user;    // 消息传递机制
        next();
    } else {                // 错误处理机制，服务器会统一处理 error
        next(new Error('Failed to load user ' + req.params.id));
    }
}

// add middleware, processed by express in order
app.use(function(req, res, next){
    req.authenticatedUser = users[0];
    next();
});

app.get('/', function(req, res){
    res.redirect('/user/0');
});

app.get('/user/:id', loadUser, function(req, res){
    res.send('Viewing user ' + req.user.name);
});

app.listen(3000);
```

## 2、error-handling

```javascript

// nearly last middleware to use, to handle invalid url path;
app.use(function(req, res, next){
    res.status(404);
    res.render('404', { url: req.url });
});

// when connect has an error, it will invoke ONLY error-handling middleware.
// If we were to next() here, any remaining non-error-handling middleware would then be executed,
// or if we next(err), only error-handling middleware would be executed.
app.use(function(err, req, res, next){
    res.status(err.status || 500);
    res.render('500', { error: err });
});
```

## 3、mount middleware with prefixPath

```javascript
var apiKeys = ['foo', 'bar', 'baz'];

function createError(status, msg) {
    var err = new Error(msg);
    err.status = status;
    return err;
}

/**
 * by mounting this middleware to "/api" prefixPath
 * meaning only req.url's paths prefixed with "/api" will cause this middleware to be invoked
 * at which point req.url is stripped when the function is invoked.
 * This affects this middleware only, subsequent middleware will see req.url as not modefied
 * unless it's mounted also.
 */

app.use('/api', function(req, res, next){
    var key = req.query['api-key'];
    if (!key) return next(createError(400, 'api key required'));
    if (apiKeys.indexOf(key) === -1) return next(createError(401, 'invalid api key'));
    req.key = key;
    next();
});
```

## 4、Cookies and Session

```javascript
// parses request cookies, populating `req.cookies` and `req.signedCookies`
// when the secret key is passed, used for signing the cookies.
// signature algorithm and secret key must only be knowned by server.
app.use(cookieParser('my secret key here'));

// Populates req.session, recognize user by signedSessionCookie.
app.use(session());
```

## 5、app.param

For example when `:user` is present in a route path, you may map user loading logic to automatically provide req.user to the route,  or perform validations on the parameter input.
```javascript
// for example: /user/:user
app.param('user', function(req, res, next, userId){
    if (req.user = users[userId]) {
        next();
    } else {
        next(new Error('failed to find user'));
    }
});
```

It is important to realize that any route that triggered a named parameter function to run will only be run if `next` was not called with an error in the named parameter handler.

## 6、content-negotiation

    res.format(object)

Performs content-negotiation on the request `Accept` header field when present. When `Accept` is not present,  the _first_ callback is invoked, otherwise the first match is used. When no match is performed the server responds with 406 "Not Acceptable". if `object.default` is defined as a `function`, then it will be invoked instead of a response of 406.

Use MIME

```javascript
res.format({
    'text/html': function(){
        res.send('hey');
    },
    'application/json': function(){
        res.send({ message: 'hey' });
    }
});
```

OR ExtName

```javascript
res.format({
    html: function(){
        res.send('hey');
    },
    json: function(){
        res.send({ message: 'hey' });
    }
});
```

## 7、vhost

```javascript
/*
edit /etc/hosts:
127.0.0.1       foo.example.com
127.0.0.1       bar.example.com
127.0.0.1       example.com
*/

var main = express();
main.get('/', function(req, res){
    res.send('Hello from main app!');
});
main.get('/:sub', function(req, res){
    res.send('requested ' + req.params.sub);
});

// Redirect app
var redirect = express();
redirect.all('*', function(req, res){
    res.redirect('http://example.com:3000/' + req.subdomains[0]);
});

// Vhost app
var app = express();
app.use(vhost('*.example.com', redirect));  // Serves all subdomains via Redirect app
app.use(vhost('example.com', main));        // Serves top level domain via Main server app
app.listen(3000);
```

## 8、single MVC

```javascript
module.exports = function (parent, options) {
    var verbose = options.verbose;

    fs.readdirSync(__dirname + '/../controllers').forEach(function (name) {
        var obj = require('./../controllers/' + name);

        var name = obj.name || name;
        var prefix = obj.prefix || '';

        var app = express();

        var method;
        var path;

        app.set('views', __dirname + '/../controllers/' + name + '/views');

        // before middleware support
        if (obj.before) {
            path = '/' + name + '/:' + name + '_id';
            app.all(path, obj.before);

            path = '/' + name + '/:' + name + '_id/*';
            app.all(path, obj.before);
        }

        for (var key in obj) {
            // "reserved" exports for other reasons
            if (~['name', 'prefix', 'engine', 'before'].indexOf(key)) continue;
            switch (key) {
                case 'show':
                    method = 'get';
                    path = '/' + name + '/:' + name + '_id';
                    break;
                case 'list':
                    method = 'get';
                    path = '/' + name + 's';
                    break;
                case 'edit':
                    method = 'get';
                    path = '/' + name + '/:' + name + '_id/edit';
                    break;
                case 'update':
                    method = 'put';
                    path = '/' + name + '/:' + name + '_id';
                    break;
                case 'create':
                    method = 'post';
                    path = '/' + name;
                    break;
                default:
                    throw new Error('unrecognized route: ' + name + '.' + key);
            }

            path = prefix + path;
            app[method](path, obj[key]);
        }

        // mount the app
        parent.use(app);
    });
};
```