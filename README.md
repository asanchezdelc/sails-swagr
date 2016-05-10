sails-swagr
=========

Originally forked from [swagger-express](https://github.com/fliptoo/swagger-express), however, heavily 
customized to meet Swagger 2.0 specifications and to work solely with [Sails](http://sailsjs.org/).

__sails-swagr__ is a simple and clean solution to integrate swagger with Sails.

### Summary

This module will do it best to autogenerate everything it can from Sails configuration and create
a Swagger 2.0 JSON Specification to be used with the Swagger-UI. After routes and models have been generated, 
you may create a `docs` directory under `api/` and place YML documents with paths definitions for each Controller. As a result, the generated JSONs and YAMLs will be **merged**. 

## Installation

    $ npm install sails-swagr --save

## Quick Start

Configure sails-swagr as express middleware.


`apiVersion`      -> Your api version.

`swaggerVersion`  -> Swagger version.

`swaggerURL`      -> Path to use for swagger ui web interface.

`swaggerJSON`     -> Path to use for swagger ui JSON.

`basePath`        -> The basePath for swagger.js

`info`            -> [Metadata][info] about the API

`apis`            -> Define your api array.

`middleware`      -> Function before response.

## Sails Integration

Create a file called express.js in your config folder and put the code:

```javascript
  var path = require('path');

  /**
   * Express Custom Middleware
   * (sails.config.middleware.cutom)
   *
   * Configure custom express middleware which will be exposed
   * automatically by Sails.
   *
   * For more information on configuration, check out:
   * https://gist.github.com/mikermcneil/6255295
   */

  module.exports.express = {
    middleware: {
      custom: true
    },

    customMiddleware: function (app) {
      var swagger = require('sails-swagr');
      var express = require('../node_modules/sails/node_modules/express');

      app.use(
        swagger.init(express, app, {
          apiVersion: '1.0',
          swaggerVersion: '2.0',
          swaggerURL: '/api/docs',
          swaggerJSON: '/api-docs.json',
          basePath: "http://localhost:1337",
          info: {
            title: ' API Doc ',
            description: 'API Services Documentation'
          },
          apis: [
            './api/docs/User.yml',
          ]
        })
      );
      app.use(app.router);
      sails.on('ready', function () {
        swagger.sailsGenerate({
          routes: sails.router._privateRouter.routes,
          models: sails.models
        });
      });
    }
  };

```

#### Swagger-UI

Lift sails and navigate to the specified `swaggerURL` e.g. 

```
http://localhost:1337/api/docs
```


## Read from YAML file

Example 'Users.yml'

```yml

paths:
  /login:
    post:
      summary: Login with username and password
      notes: Returns a user based on username
      responseClass: User
      nickname: login
      consumes: 
        - text/html
      parameters:

      - name: username
        dataType: string
        paramType: query
        required: true
        description: Your username

      - name: password
        dataType: string
        paramType: query
        required: true
        description: Your password

definitions:
  User:
    properties:
      username:
        type: String
      password:
        type: String    
```

[Swagger](https://developers.helloreverb.com/swagger/) is a specification and complete framework 
implementation for describing, producing, consuming, and visualizing RESTful web services.
View [demo](http://petstore.swagger.wordnik.com/).

# Credits

- [Express](https://github.com/visionmedia/express)
- [swagger-jack](https://github.com/feugy/swagger-jack)

## License

(The MIT License)

Copyright (c) 2015 qbanguy &lt;heyadrian@gmail.com&gt;

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
