---
layout: post
title: "A simple Silex Application Skeleton"
date: 2014-08-30 23:51:49 +0200
comments: true
riassunto: "Developing a starter project for my Silex applications. Take one."
description: "Silex doesn't need a fixed structure, so you have to develop one. I have done so and here I explain how"
categories: silex php
---

I have set up a [skeleton for Silex applications][2] that use Twig as a template library.

## Details

It all starts with `web/index.php`

```php
<?php

require __DIR__ . '/../vendor/autoload.php';

$app = require_once __DIR__ . '/../src/app.php';
require_once __DIR__ . '/../config/prod.php';
require_once __DIR__ . '/../src/controllers.php';

$app->run();
```

First, the app is created in `src/app.php` and the Twig Service Provider is loaded:

```php
<?php

use Silex\Application;
use Silex\Provider\TwigServiceProvider;

$app = new Application();
$app->register(new TwigServiceProvider());

return $app;
```

Then, in `config/prod.php`, we set the Twig path (and other settings):

```php
<?php

$app['twig.path'] = __DIR__ . '/../templates';
```

Finally, in `src/controllers.php` we load the routes:

```php
<?php

$app->get('/', function() use ($app) {
    return $app['twig']->render('hello.twig', array(
        'name' => 'World',
    ));

});
```

### Thanks to

This project is based on [Silex-Skeleton][1]


[1]: https://github.com/silexphp/Silex-Skeleton
[2]: https://github.com/JustB/silex-skeleton