# Service Container

## Introduction

The first iteration of Horizon did not have a service container, and its service providers were classes that would
return any type of data upon invocation. To help keep things organized for the future, the service container was
created.

Very simply, the service container registers service providers and provides an interface to "make" instances from those
providers on demand. Horizon has a global, application-level service container that registers all service providers
automatically. You can also create your own container instances.

You can retrieve the application's service container using the `app()` global helper function.

## Registering Providers

Containers do not automatically look for and register providers. For the application's global container, this is done by
the kernel. If you're working with your own container or would like to manually extend the global container, you can pass
instances of `ServiceProvider` to the container.

```php
$provider = new MyServiceProvider();
app()->register($provider);
```

**Warning:**
Providers which are not deferred will not be automatically booted by the container when registered manually. Be sure to
call `boot()` before registering them in such cases.

## Resolving Instances

Horizon's service container is capable of providing both a single instance from the last-registered provider of that
instance, or a collection of instances from all providers of that instance.

### Singletons

In most cases, you'll want to resolve a single instance of a dependency. You can use the `make()` method to do this.

```php
$instance = app()->make('App\Dependency');
```

For singletons you can also use the `resolve()` helper function as a shortcut.

```php
$instance = resolve('App\Dependency');
```

### Collections

In some cases, you may want to retrieve all instances from all providers of a class. This is useful, for example, when
loading extensions, views, and translations, which often have multiple files and can be found in many source directories.
Using the `all()` method, we can retrieve a `ServiceObjectCollection`.

```php
$collection = app()->all('App\Dependency');

$instances = $collection->all(); // Get an array of instances
$first = $collection->first(); // Get the first instance

while ($instance = $collection->next()) // Iterate using next()
foreach ($collection as $instance) // Iterate using foreach()
```
