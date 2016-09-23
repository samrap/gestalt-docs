# Loaders

So far in the examples, we have been creating our Configuration by passing in an array to its constructor. While array-based configuration values work great once loaded into a Configuration object, it is likely you have a more robust way of storing these values. Gestalt's Loaders provide a seamless way of translating your configuration values to create a Configuration object.

### Callback Loaders

Callback Loaders allow you to create a Configuration object using a callback:

```php
$config = Configuration::load(function() {
    $items = parse_ini_file('config/app.ini', true);

    return $items;
});
```

The callback passed to the `load` method must return an array of configuration values. This array will be used to create a new instance of the Configuration object. In this case, we parse an ini file into an array and return it. Let's say our `app.ini` file looks like this:

```ini
[app]
debug = true
```

Once loaded into the object, calling `$config->get('app.debug')` will yield the boolean `true`.

---

**Tip:** Callback Loaders are great for simple configuration loading but can become difficult to maintain with more complex logic. If you find your callback is becoming too long, consider Class Loaders, explained below. 

---

### Class Loaders

Class Loaders come in handy when you need to define more complex functionality for loading your configuration values. Maybe you want to iterate over a directory to pull multiple files, or perhaps retrieve the values from an API endpoint. Class Loaders allow you to encapsulate this logic and even swap out implementations without altering the rest of your code.

Class Loaders must implement the `Gestalt\Loaders\LoaderInterface` interface and its single `load` method. Identical to Callback Loaders, a Class Loader's `load` method must return an array of configuration values. This array will be used to create a new instance of the Configuration object. Let's look at defining a Class Loader:

```php

namespace App\Configuration;

use DirectoryIterator;
use Gestalt\Loaders\LoaderInterface;

class IniDirectoryLoader implements LoaderInterface
{
    /**
     * Load the configuration items and return them as an array.
     *
     * @return array
     */
    public function load()
    {
        $items = [];
        $directory = new DirectoryIterator(realpath('/config'));

        foreach ($directory as $file) {
            if ($file->isFile() && $file->getExtension() == 'ini') {
                $filename = $file->getFilename();
                $config = substr($filename, 0, strrpos($filename, '.'));

                $items[$config] = parse_ini_file($file->getPathName(), true);
            }
        }

        return $items;
    }
}

```

In this example, we loop through a `config` directory and create an associative array from all of the parsed ini files. Finally, we return the array. Going back to where we create the Configuration object, we can see how to use this new Class Loader:

```php
$config = Configuration::load(new IniDirectoryLoader);
```

By passing an instance of `IniDirectoryLoader`, it's `load` method will be called and the resulting array used to instantiate a new Configuration object.

---

**Did you know?** Gestalt comes packaged with a few helpful Class Loaders right out of the box. These loaders are located in the `Gestalt\Loaders` namespace.

---
