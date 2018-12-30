# Autoloader

## Introduction

Horizon's autoloading is more complicated than other frameworks due to its support for shared hosting, extensions, and
remote updates. To avoid relying too heavily on Composer, it has its own autoloader.

## Composer

### Application vendors

Applications which need Composer packages should have their own `app/composer.json` file. The framework automatically
requires the `app/vendor/autoload.php` file if it exists. Remember to never modify files inside the horizon directory.

### Extension vendors

Extensions should have their own Composer installations and vendor directories. Note that the vendor autoloaders are
_not_ automatically required. Instead, you can specify the autoloader file via the `files` option in the extension. For
example:

```php
'files' => array(
    'vendor/autoload.php'
)
```

For more information on extensions and their configuration, refer to the
[extensions documentation](../essentials/extensions.md).

## Namespaces

The `config/namespaces.php` file specifies namespaces which will be "mounted" during startup. This file can be used to
easily create new namespaces pointing to different source directories. The `App` namespace is registered through this
file by default as well.

**config/namespaces.php:**
```php
return array(
    'map' => array(
        'App\\' => '/app/src'
    )
);
```

### Manual mounting

To mount a namespace programatically, you can use the static `Autoloader` service. This is a foundation facade and its
interface will not change in minor updates. You can mount namespaces at any point in the application's execution – the
mapping will take effect immediately.

```php
use Horizon\Foundation\Services\Autoloader;

Autoloader::mount('App\\', '/absolute/path/to/src');
```
