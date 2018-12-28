@title Client

# Introduction

This page gives an overview on how to add update repositories to your application, and how to check for and
install updates programatically. Before following this guide, ensure that you have set up a [repository](repositories.md).

---

# Configuration

The configuration file at `app/config/updates.php` contains various important settings which you, the developer, should
ensure are configured appropriately for your application.

Some notable configuration keys include:

- `certificate_authority`:<br>
   Path to a certificate authority bundle for verifying certificates. A default authority is built into the framework
   which should usually suffice.

- `peer_validation`:<br>
   Enables SSL handshakes using the authority bundle. This is recommended if you're serving updates over HTTPS protocol.

- `enforce_security_policy`:<br>
   Prevents updates from modifying, or in any way accessing, files outside of their mounted directories.

---

# Service Provider

Adding a repository to your app requires a provider. As expected, you can add or remove providers in the `providers.php`
configuration file.

By default, the `Horizon\Provider\Services\UpdateProvider` provider is active for updates. This allows core framework
updates into the `/horizon` directory.

It's very easy to create an update provider. It looks like this:

```php
class CustomUpdateProvider extends Horizon\Provider\ServiceProvider
{
    /**
     * Returns an array of update repositories.
     *
     * @return Repository[]
     */
    public function __invoke()
    {
        $url = 'https://updates.bailey.sh/horizon';

        $repo = new Horizon\Updates\Repository($url);
        $repo->setChannel('jupiter');
        $repo->setMountPath('/horizon');
        $repo->setCurrentVersion(\Horizon::VERSION);

        return array($repo);
    }
}
```

## Mounting

In the example above, `setMountPath()` is used. A mount path serves two purposes. First, it provides the directory into
which files are extracted. Second, it creates a constraint which only allows updates to access files or directories within
that path.

---

# Getting updates

To check for updates, call the `getLatestUpdates()` static method. This returns a collection of available updates.

```php
use Horizon\Updates\UpdateService;

$collection = UpdateService::getLatestUpdates();

foreach ($collection as $update) {
    $version = $update->getVersion();
    $changes = $update->getCommits();

    echo "An update to version {$version} is available.";
}
```

---

# Installing updates

## Creating backups

Call the `backup()` method on an update to automatically create an encrypted zip archive containing the current state of
all files which will be affected by this update.

You can treat the returned `ZipArchive` as a string and save it anywhere.

```php
$zip = $update->backup();

// For instance, add files to it
$zip->createFile('contents', 'path/to/file.txt');

// Or, save it on the disk
file_put_contents('backup.zip', $zip);
```

## Running scripts

Call the `install()` method on an update to execute its upgrade script. This will perform extraction, database queries,
and any other scripted commands.

If there is an issue with any part of the process, an `UpdateException` will be thrown.

```php
try {
    $update->install();
}
catch (\Horizon\Updates\UpdateException $e) {
    echo "Error: {$e->getMessage()}";
}
```

---

# Handling exceptions

Each and every component of the update process has the potential to throw an `UpdateException` if things go wrong.
Unfortunately, these exceptions may not always be very detailed.

## Getting logs

To help with debugging, the update utility logs each step in detail. You can retrieve the full log at any time as a
string (new lines separated with `\n`).

Note that this log is global, meaning log information from all updates will be present.

```php
$logs = UpdateService::getLog();
```