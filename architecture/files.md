# File Structure

## Application

The `/app/` directory stores all application files, such as libraries, packages, source code, views, and extensions. It
is the primary workstation of any Horizon application.

|Directory|Description|
|---|---|
|`/config`|Project configuration files|
|`/errors`|Custom HTTP error pages|
|`/extensions`|Themes, plugins, etc|
|`/public`|Public assets like images and styles|
|`/translations`|Translation files|
|`/routes`|Route declaration files|
|`/src`|Project source code and libraries|
|`/views`|Hardcoded view templates|
|`/vendor`|Optional extra composer packages|

### Composer

For projects which depend on Composer packages, you can add dependencies to `/app/composer.json` and generate a vendor
directory at `/app/vendor/`. The framework will automatically recognize the existence of the vendor directory and will
import the autoloader on startup.

## Configuration

The `/app/config/` directory stores configuration files.

|File|Configuration|
|---|---|
|`app.php`|Core app settings|
|`console.php`|Console usage|
|`database.php`|Databases|
|`errors.php`|Error handling|
|`namespaces.php`|Namespace mapping and autoloading|
|`providers.php`|Registered service providers|
|`session.php`|User sessions and cookies|
|`updates.php`|Automatic updates|

## Horizon

The `/horizon/` directory is where internal framework libraries are stored. It also hosts a composer vendor directory,
a testing suite, default error pages, and other vital resources.

**Danger:**
Do not edit or directly depend on files in this directory, as they are subject to change in future updates.
