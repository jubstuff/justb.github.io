---
layout: post
title: "Working with Strongloop's loopback"
date: 2014-01-17 12:24:18 +0100
comments: true
categories:
---

So I am working with Loopback by Strongloop.

## Creating models

To create a model, you simply place a .js file in the models folder. After calling app.boot, loopback will load all .js
files automatically. But how to reference those models?

```javascript
//models/product.js
var Model = require('loopback').Model;
var Product = Model.extend('product');
```
```javascript
//Anywhere else
var loopback = require('loopback');
var Product = loopback.getModel('product');
```

## Connecting to a MongoDb Database

To create a connection with a mongodb database (or for any other datasource, just replace mongo with its name), you have
several choices.

First, you can create it programmatically, using the `loopback.createDataSource` method. This method accepts a
JavaScript object with these properties:

```javascript
loopback.createDataSource({
  connector: require('loopback-connector-mongodb'),
  host: 127.0.0.1,
  port: 27017,
  database: 'myDatabaseName',
  username: 'myDatabaseUser',
  password: 'myPassword'
});
```

Otherwise, you can specify your datasource in the `datasources.json` file in the root directory

```javascript
{
  "db": {
    "defaultForType": "db",
    "connector": "mongodb",
    "host": 127.0.0.1,
    "port": 27017,
    "database": 'myDatabaseName',
    "username": 'myDatabaseUser',
    "password": 'myPassword'
  }
}
```
