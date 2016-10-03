# Introduction

All of Gestalt's functionality revolves around a single object: `Gestalt\Configuration`. The Configuration object is intended to represent your framework's configuration values as a "collection", similar to Laravel or other collection objects.

### Basic Usage

Let's take a look at a Configuration object in its simplest form:

```php
use Gestalt\Configuration;

$config = new Configuration(['debug' => true]);
```

A Configuration can be thought of as a collection of your configuration values. It is instantiated via an array of key-value pairs that contain your framework or application's configuration values.

---

**Did you know?** Gestalt's constructor also accepts a `Configuration` instance or an instance of PHP's `Traversable` interface.

---

Retreiving Configuration values is natural and concise:

```php
$debug = $config->get('debug');
```

Retreive a value, passing a default value to return if the item does not exist:

```php
$debug = $config->get('debug', false);
```

Alternatively, you may access the values as an array:

```php
$debug = $config['debug'];
```

Using the `get` method you may also use dot notation to retrieve values from a nested Configuration:

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

At some point you may wish to reset the Configuration back to the original values it was created with. This can be done at any time by using the `reset` method:

```php
$config->reset();
```

The `reset` method returns an instance of itself, allowing you to chain additional method calls:

```php
$config->reset()->add('log', true);
```

---

**Tip:** Be careful when using the `set` and `add` methods. It is easy to lose track of the state of your Configuration if these are abused, especially in conjunction with the `reset` method.

---

To retrieve the entire array of configuration values, use the `all` method:

```php
$environment = $config->all();
```

### Configuration Prefixes

Occasionally, you may wish to modify multiple values in a nested area of your Configuration. Traditionally, that would look something like this:

```php
$configuration->set('database.drivers.mysql.username', 'bonnie');
$configuration->set('database.drivers.mysql.password', 'martini');
```

Although this works just fine, typing the same "prefix" over and over can become repetitive. It would be nice if we could type the prefix once and make multiple changes within it. Configuration Prefixes let you do just that. Let's look at an example:

```php
$configuration->prefix('database.drivers.mysql', function ($partial) {
    // Modifies the 'database.drivers.mysql.username' value.
    $partial->set('username', 'bonnie');

    // Modifies the 'database.drivers.mysql.password' value.
    $partial->set('password', 'martini');
});
```

The Configuration's `prefix` method takes two arguments. The first argument is the value in which to prefix Configuration calls. The second argument is a callback function which accepts the prefixed Configuration object. Because it is a Configuration instance, all of the standard methods are available. 

After using the `prefix` method above, we can access `database.drivers.mysql.username` and see "bonnie" as the value:

```php
echo $configuration->get('database.drivers.mysql.username');
// "bonnie"
```

---

**Note:** When using a Configuration Prefix, any attached Observers will be notified only once and regardless of the methods called within the function. This is due to the implementation of prefixes and may be changed down the line.

---
