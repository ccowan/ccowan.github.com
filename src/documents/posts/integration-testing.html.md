---
title: "Integration Testing"
layout: "post"
tags: ["node.js","testing"]
---

I hate hate hate testing. It's not that I don't know how to write tests or that I don't want to write tests, it's just that the setups I've used in the past have been totall cumbersome. I finally found a sensable setup for integration testing that lets me setup tests to depend on each other.

Imagine your writing a set of tests where you have to create a new user, validate date that user by visiting a vaildation link (like you would find in an email). Then use that valid user to login and post a new message.


```javascript
var test = require('tape');
var request = require('request');
var helpers = request('./helpers');

var run = module.exports = function (callback) {
  callback = callback || function () { };
  test('/api/v1/user', function (t) {
    var options = {
      url: 'http://localhost:6767/api/v1/login',
      method: 'POST',
      body: {
        login: 'joeblow',
        password: 'mysecret'
      }
      json: true,
      jar: helpers.jar
    };

    require(options, function (err, resp, body) {
      t.equals(resp.statusCode, 200);
      t.end();
      callback(err, resp, body);
    });
  });
};

if (require.main === module) {
  run();
}
```
