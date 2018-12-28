# Service Providers

## Introduction

Horizon implements a basic service provider which is used by the [service container](container.md) to instantiate
objects for services that need them. Internally, they are used for views, routes, components, translations, and so on.
It is also possible to utilize providers for your own applications as well, so having an understanding of this concept
is fairly important.

You can see all of the registered providers in the `config/providers.php` file. By default, the core essentials are in
this file, but you can remove them and write your own to change how the framework functions.

## Writing Service Providers

You can create a class anywhere in your source code and extend the core service provider class to get started. The
following code sample shows the basic methods you'll be implementing.

**/src/Providers/CustomProvider.php:**
```php
namespace App\Providers;

use Horizon\Support\Services\ServiceProvider;

class CustomProvider extends ServiceProvider
{
    /**
     * Boots the service provider.
     */
    public function boot()
    {

    }

    /**
     * Gets a list of all class names that the service provider can provide.
     *
     * @return string[]
     */
    public function provides()
    {
        return array();
    }

    /**
     * Registers the bindings in the service provider.
     */
    public function register()
    {

    }
}
```

### The Boot Method

The `boot()` method is called by the framework's kernel as soon as the service provider is loaded. This occurs before
any routes or commands executed, and should be used as a bootstrap method.

### The Provides Method

When the service container is loading a provider, the `provides()` method will be called. An array of class names which
the provider is capable of providing should be returned. The container will keep record of these classes and will
invoke your provider when an instance of those classes is required.

In the following example, our provider informs the container that it can provide objects of the `Car` and `Bike` classes
upon request.

```php
public function provides()
{
    return array(
        'App\Vehicles\Car',
        'App\Vehicles\Bike'
    );
}
```

### The Register Method

The `register()` method allows binding a provided class name to a callable which will return an instance of that class.
To do this we use the protected function `bind()`. If for some reason you are unable to provide an instance from within
the callable, you can return `null`.

```php
public function register()
{
    // We can return a single instance
    $this->bind('App\Vehicles\Car', function() {
        return new Car('2018 Honda Civic', 'Blue');
    });

    // We can also return an array of instances
    $this->bind('App\Vehicles\Bike', function() {
        return array(
            new Bike('Kawasaki Ninja 300', 'Green'),
            new Bike('Kawasaki Ninja 300', 'Red')
        );
    });
}
```

## Registering Providers

All providers are registered in the `config/providers.php` file. To register a new provider, you only need to add its
class name to the array.

**/config/providers.php:**
```php
return array(
    'Horizon\Routing\RoutingServiceProvider',
    'Horizon\View\ViewServiceProvider',
    'Horizon\Extension\ExtensionServiceProvider',
    'Horizon\Translation\TranslationServiceProvider',
    'Horizon\Updates\UpdateServiceProvider',
    'App\Providers\CustomProvider'
);
```

**Note:**
It is possible to use the `::class` resolver instead of strings. However, strings are used by default to keep the
framework compatible with PHP 5.4.

## Deferred Providers

The `boot()` method is called for all providers as soon as they are loaded into the container. This happens immediately
after autoloading namespaces are mapped – in other words, very early.

However, it's possible to defer the provider so that it only boots when it's actually needed. This can help save
resources if the provider doesn't always need to be running.

To defer a provider, simply set the protected member `$defer` to `true`.

```php
class CustomProvider extends ServiceProvider
{
    protected $defer = true;

    // ...
}
```
