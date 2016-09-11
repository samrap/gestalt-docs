# Introduction

All of Gestalt's functionality revolves around a single object: `Gestalt\Configuration`. The Configuration object is intended to represent your framework's configuration values as a "collection", similar to Laravel or other collection objects.

Let's take a look at a Configuration object in its simplest form:

```php
use Gestalt\Configuration;

$config = new Configuration(['debug' => true]);
```

A Configuration can be thought of as a collection of your configuration values. It is instantiated via an array of key-value pairs that contain your framework or application's configuration values.

Retreiving those values is natural and concise:

```php
$debug = $config->get('debug');
```

Alternatively, you may access the configuration values as an array:

```php
$debug = $config['debug'];
```

Of course, you may use dot notation to retrieve values from a nested Configuration:

```php

$config = new Configuration([
    'app' => [
        'debug' => true,
        'url' => 'example.com',
    ],
]);

$debug = $config->get('app.debug');
```

Adding new values is equally simple:

```php
$config->add('locale' => 'en');
$config->add('mail.driver', 'MailMonkey');
```

In the second example, we use dot notation to add a new item to the Configuration. Each dot represents a new dimension in the array.

The `add` method will add new values, but do nothing if the item already exists. To modify an existing item, use the `set` method instead:

```php
$config->set('debug', false);
```

To retrieve the entire array of configuration values, use the `all` method:

```php
// Get the whole enchilada.
$enchilada = $config->all();
```

At some point you may wish to reset the Configuration back to the original values it was created with. This can be done at any time by using the `reset` method:

```php
$config->reset();
```

The `reset` method returns an instance of itself, allowing you to chain additional method calls:

```php
$config->reset()->add('log', true);
```

---

**Tip:** Be careful when using the `set` and `add` methods. It is easy to lose track of the state of your configuration if these are abused, especially in conjunction with the `reset` method.

---
