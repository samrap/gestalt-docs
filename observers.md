# Observers

Gestalt's Observers provide a simple way to listen for changes on a Configuration object. By implementing the Observer Pattern, Gestalt allows you to attach one or many listeners to the Configuration object and perform operations whenever the Configuration is updated.

### Creating an Observer

Observers implement the `Gestalt\Util\ObserverInterface` interface and define a single `update` method. This method takes an instance of the Configuration and is called every time it is updated, giving you full access to the Configuration in real time. Let's define and implement a potential use case of using Observers in Gestalt.

It is likely that your configuration settings are stored in some sort of stateful manner. Whether it is a group of JSON files, YAML files, or even stored in the database, there may be a time when you wish to persist the changes made in your application to your stateful configuration. Gestalt has no working knowledge of where your configuration values come from, but it does help you quite a bit by using Observers. Let's take a look at defining a basic observer:

```php
use Gestalt\Util\Observable;
use Gestalt\Util\ObserverInterface;

class ConfigurationObserver implements ObserverInterface
{
    public function update($config)
    {
        $this->file->write($config->all());
    }
}
```

In this example we have some `$file` object which is a property of our Observer. The Observer implements the `ObserverInterface` and defines an implementation of the `update` method, which takes a Configuration object. Each time the observer's `update` method is called, it writes the updated Configuration to a file.

### Subscribing an Observer

Now that we have defined our Observer, we should attach it to the Configuration so that it will be notified of the changes:

```php
$config = new Configuration(['debug' => true]);
$config->attach(new ConfigurationObserver);
```

Now whenever we `add` or `set` items on the Configuration, our ConfigurationObserver's `update` method will be called with the updated object and the file will be written to. 

This is just one use case, but you may want to perform multiple different actions when the Configuration changes. You can attach any number of Observers to the Configuration allowing you to do just that:

```php
$config->attach(new ConfigurationObserver);
$config->attach(new QueueConfigurationObserver);
```

You can also unsubcribe Observers at any time using the `detach` method:

```php
$config->detach($observer);
```

The `reset` method does not automatically notify the Configuration's observers, but you can chain the Configuration's `notify` method to manually notify the subscribed Observers after a reset:

```php
$config->reset()->notify();
```
