social-cms-backend
==================

Express middleware to provide schema-less REST APIs for creating a social networking website primarily using angular.js. It comes with built-in authentication, authorization and notification features.

Motivation
----------

There exists several MVC framework libraries for node.js
that are inspired by Rails.  But they might be a bit outdated,
when it comes to angular.js, client-side MVW framework.
I would like to propose a maybe new style of web programming,
which is the combination of a domain-specific REST API library
(ready to use, no coding required) and client-side coding.

This project is to provide such a library for a web site
like SNS/Twitter/Facebook in a closed/private environment.

How to install
--------------

    $ npm install social-cms-backend

How to use
----------

    var express = require('express');
    var SCB = require('social-cms-backend');
    var app = express();
    app.use(SCB.middleware({
      mongodb_url: 'mongodb://localhost:27017/socialcmsdb',
      passport_strategy: 'facebook',
      facebook_app_id: process.env.FACEBOOK_APP_ID,
      facebook_app_secret: process.env.FACEBOOK_APP_SECRET
    }));
    app.listen(3000);

With socket.io v1.0

    var http = require('http');
    var express = require('express');
    var socket_io = require('socket.io');
    var SCB = require('social-cms-backend');
    var app = express();
    var SCB_options = {
      mongodb_url: 'mongodb://localhost:27017/socialcmsdb',
      passport_strategy: 'facebook',
      facebook_app_id: process.env.FACEBOOK_APP_ID,
      facebook_app_secret: process.env.FACEBOOK_APP_SECRET
    };
    app.use(SCB.middleware(SCB_options));
    var server = http.createServer(app);
    var sio = socket_io(server);
    sio.use(SCB.socket_io(SCB_options));
    server.listen(3000);

With HTTP DIGEST strategy:

    var SCB_options = {
      mongodb_url: 'mongodb://localhost:27017/socialcmsdb',
      passport_strategy: 'digest',
      auth_digest: {
        realm: 'my_realm'
      }
    };

With BreezeJS support:

    var SCB_options = {
      mongodb_url: 'mongodb://localhost:27017/socialcmsdb',
      breeze_mongo: true,
      routes: [{
        object_type: 'user',
        object_prefix: '/breeze-service/users'
      }, {
        object_type: 'post',
        object_prefix: '/breeze-service/posts'
      }, {
        object_prefix: '/breeze-service/SaveChanges'
      }]
    };

REST APIs
---------

By default, there are 4 object types:
* post
* user
* group
* like

The following is the example of the post object endpoint.

### List post objects

    GET /posts?query=...

The "query" query parameter is a MongoDB query parameter object
that is stringified (probably by JSON.stringify).
* `skip` and `limit` query parameters are also supported.

### Get one post object

    GET /posts/123

The "123" is the `_id` of the post.

### Save a new post

    POST /posts

The body is an object (JSON format) without system preserved properties such as
`_id`, `system`, `created_time`, `owner`, `meta`.

### Update a post

    PUT /posts/123

The body is a MongoDB update object (JSON format) using update operators.

### Delete a post

    DELETE /posts/123

### Count posts

    GET /posts/count?query=...

This is a special endpoint.

### Get following posts

    GET /posts/inbox

This is a special endpoint to only get posts that matches with predefined "following".
More description follow in the next section.

### Aggregate Posts

    GET /posts/aggregate?pipeline=...

This is a special endpoint to use MongoDB aggregate command.
The "pipeline" query parameter is a MongoDB pipeline parameter object
that is stringified (probably by JSON.stringify).

### A special User endpoint

(this is not a post endpoint.)

    GET /users/myself

This is to get current login user information.


Object Scoping
--------------

TODO
* `scope` attribute for access control

Push notification (socket.io)
-----------------------------

TODO
* `destination` attribute for push (socket.io)

Extension to JSON format
------------------------

TODO
* special string format for `Date` and `RegExp` types

Screencast
----------

### How to create a Twitter clone in 15 minutes

Screencast preview (quadruple speed):

![Preview](http://dai-shi.github.io/social-cms-backend/ttyrecord.gif)

<a href="http://dai-shi.github.io/social-cms-backend/ttyplay.html" target="_blank">Controllable screencast at normal speed</a>

Notes:

* There is a typo found after the recording.
  `/javascript/main.js -> /javascripts/main.js`
* The resulting code is available
  [here](https://github.com/dai-shi/twitter-clone-sample/tree/20130804_recorded)
* You can try the running web service of the code
  <a href="http://twitterclonesample-nodeangularapp.rhcloud.com/" target="_blank">here</a>

TODOs
-----

* Notification: email
