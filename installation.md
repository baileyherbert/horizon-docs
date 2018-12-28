# Installation

## Manual Installation

The latest release can be fetched from the repository on [GitHub](https://github.com/baileyherbert/horizon/releases).
Download these files and extract them into your project's public directory. Next, open a terminal in the same directory,
and run this command:

```
composer install -d horizon
```

## Configuration

You can find the configuration files in the `/app/config/` directory. Take a moment to look through the available
files and options. Most options are documented in detail.

|File|Description|
|---|---|
|`app.php`|Changes core app settings applied by the framework.|
|`database.php`|Sets the database configuration.|
|`namespaces.php`|Customizes the class autoloader.|
|`providers.php`|Sets service providers.|
|`session.php`|Changes the behavior and functionality of sessions.|
|`updates.php`|Configures settings for the update library.|

## Deployment

If you're migrating the application (for example from testing to a production server), you can package all files
into an archive and then extract the archive on the remote server.

If you skip copying of the vendor directories, you must use the commands as described [above](#manual-installation).
