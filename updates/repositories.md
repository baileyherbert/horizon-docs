@title Repositories

# Introduction

A repository is a directory on a web server which hosts update files, scripts, and metadata. These must follow a very
specific format in order to work.

To create your first repository, create an empty subdirectory somewhere on your server. As an example, the default
repository is at the following link. In this link, `horizon` is the repository.

```
https://updates.bailey.sh/horizon/
```

In the repository directory should exist a `repository.json` file. The file will define the different channels which
are hosted by it (e.g. stable, development, nightly). Here's an example.

@code {
    @file https://updates.bailey.sh/horizon/repository.json

    ```json
    {
        "server": {
            "name": "Horizon Updates",
            "url": "https://updates.bailey.sh/horizon"
        },
        "channels": [
            {
                "name": "jupiter",
                "description": "The stable branch for Horizon Jupiter."
            }
        ]
    }
    ```
}

---

# Channels

Each channel defined in the `repository.json` meta file must exist as a subdirectory of the repository, with a matching
name. Inside the channel must exist a JSON file, with a name matching the channel's name. For a channel named "jupiter",
our meta file is called `jupiter.json`.

This meta file defines the versions available in the channel and any associated details.

@code {
    @file https://updates.bailey.sh/horizon/jupiter/jupiter.json

    ```json
    {
        "versions": [
            {
                "version": "1.0.0",
                "type": "release",
                "commits": [
                    "Initial commit"
                ]
            },
            {
                "version": "1.0.1",
                "type": "bug_fix",
                "commits": {
                    "Bugs squashed": [
                        "Fixed the login page not redirecting properly",
                        "Fixed the admin panel not redirecting to login"
                    ]
                }
            }
        ]
    }
    ```
}

---

# Versions

Each version defined in the channel meta file should have its own directory alongside that meta file. These directories
should be named the version number.

```
https://updates.bailey.sh/horizon/jupiter/1.0.0/
https://updates.bailey.sh/horizon/jupiter/1.0.1/
```

Inside the version directories there must be two files. Neither of these files have extensions.

- `payload` is the compressed (zipped) archive containing update files.
- `upgrade` is a plain text script file with update instructions.

The payload will not be extracted without a working `upgrade` script. To learn how to write such scripts, refer to the
[Scripting](scripting.md) page.

---

<!--
# Server

## Manifest

The server manifest is a JSON file in the root directory of your update path, which tells us the latest version for each channel. The file should be named `manifest.json` and
follow this format:

@code {
    @file /manifest.json

    ```json
    {
        "channels": {
            "release": "1.0.1"
        }
    }
    ```
}

This manifest tell us that the latest update is at `release/1.0.1/` for users who are subscribed to the `release`
update channel.

@tip:info {
    Channels are useful if you wish to provide different release branches (i.e. stable, beta, nightly).
}

## Updates

### Manifest

An update manifest file must also be present for each version. These will be named `manifest.json` and must exist in the root
of the version's directory.

@code {
    @file /release/1.0.1/manifest.json

    ```json
    {
        "version": "1.0.1",
        "requires": "1.0.0",

        "details": {
            "title": "Release 1.0.1",
            "description": "This update fixes a few small bugs.",
            "date": "08/17/2018 16:12:00 UTC"
        }
    }
    ```
}

@tip:info {
    The framework will traverse the `requires` field, if it is provided, to find all available updates.
}

### Guide

In the same directory as the update manifest must be a plain-text file named `guide`. This file uses a basic syntax to
instruct the update procedure. Here's some example syntax:

@code {
    @file /release/1.0.1/guide

    ```php
    // Ensures the downloaded payload equals the specified md5 hash
    verify "85a2f1da8515d476ce19c98c892f5128"

    // Backup files on the server
    backup "app/src/*"
    backup "horizon/*"
    saveas "backup-%version-%m-%d-%y.zip"

    // Or, you can automatically backup all files this guide is set to modify
    backup staged
    saveas "backup-%version-%m-%d-%y.zip"

    // Delete existing files
    delete "app/oldfile.php"
    delete "app/old/*"
    delete "app/storage/*.old.zip"

    // Other operations
    mkdir "some/dir"
    chmod "some/dir" 0777

    // Append 'try' before an operation to make it ignore any errors
    try mkdir "some/dir"
    try chmod "some/dir" 0777

    // Payload operations
    extract "app/newfile.php"
    extract "app/newdir/"
    extract "app/migrations/"
    extract "changelogs/1.0.1/*" into "app/changelogs/1.0.1"
    extract "changelogs/1.0.1/" to "app/changelogs/1.0.1"
    extract "changelogs/archive.zip" to "app/changelogs/archive.zip"

    // Upgrade the users table via migrations
    migrate up users

    // Run simple queries
    query "UPDATE settings SET someSetting = 0"

    // Execute php files inside the payload
    execute "db_upgrade.php"

    // Require the above execution to return true
    require true

    // Or, if it doesn't return true, throw an error
    require true "It didn't return true."
    ```
}

### Payload

Alongside these `guide` and `manifest.json` files is the `payload`. This is simply a compressed .zip file without the
extension.

## Security

You can secure updates by putting them behind a keywall. The server-side implementation of this is up to you, but when
a key is provided by the client, it is sent as an HTTP header on all requests.

```
X-UpdateKey: TheKeyGoesHere
```

Your server should check the key and return an `HTTP 403` error if it is not valid. Otherwise, return the resources as
expected.

---

# Client

## Automatic Updates

Horizon ships with a built-in update system. By default, it checks for minor framework updates. You can configure this
in your `providers.php` configuration file, or [create your own update provider](../essentials/providers.md#updates).

The only thing you need to implement in your app for this is the actual calls for update checking and installation.

```php
use Horizon\Updates\UpdateKernel;
use Horizon\Updates\UpdateException;

// Check for updates
UpdateKernel::check(true);

// Optional - Get an array of updates
// See the "Getting Details" section below for usage
$updates = UpdateKernel::fetch(true);

// Install updates
try {
    UpdateKernel::install(true);
}
catch (UpdateException $e) {

}
```

@tip:info {
    The code example above includes a `true` argument in each method. This enables database saving/loading,
    and will automatically create a table called `updates` with the appropriate schema.
}

## Fetch Updates

Use the update client and provide the absolute URL to your main manifest. Remember to use `https://` when possible. The
framework will use its internal CA bundle to verify the certificate.

```php
use Horizon\Updates\Client;

$client = new Client('https://updates.mysite.org/app/manifest.json');
$client->setCurrentVersion('1.0.0');
$client->setChannel('release');

$updates = $client->fetch();

foreach ($updates as $update) {
    $title = $update->getTitle();
    $version = $update->getVersion();
}
```

## Install Updates

The `install()` method automatically follows the update guide to perform an installation, but you must first configure
the update appropriately.

```php
// Install updates relative to the framework's root dir
$update->setRootDirectory(Horizon::ROOT_DIR);

// Save backups to the specified directory
$update->setBackupDirectory(Path::join(Horizon::ROOT_DIR, 'app/backups'));

// Undo any file operations when an error occurs
$update->setRevertOnError(true);
```

Now you can install the update with appropriate error handling.

```php
try {
    $update->install();
}
catch (UpdateException $e) {
    echo "Update failed due to an error: " . $e->getMessage();
}
```

## Getting Details

You can use these methods on an update to get information about it.

```php
// Before installing

int      $update->getTimestamp();

array    $update->getManifest();

string   $update->getTitle();
string   $update->getDescription();
string   $update->getVersion();
string   $update->getPreviousVersion();
string   $update->getChannel();

string   $update->getPayloadUri();
string   $update->getManifestUri();
string   $update->getGuideUri();

bool     $update->doesRunQueries();
bool     $update->doesExecuteFiles();

string[] $update->getFilesToModify();
string[] $update->getFilesToDelete();

// After installing

string[] $update->getWrittenFiles();
string[] $update->getDeletedFiles();
string[] $update->getCreatedDirectories();
string[] $update->getDeletedDirectories();
string[] $update->getBackups();

double   $update->getTimeTaken();
```

## Keys

To interact with an update server protected behind a keywall, setting the key can be done as shown below. It will be
sent along with each further request.

```php
$client->setKey('TheKeyGoesHere');
```

---

# Error Handling

## Codes

Updates can get complex and many errors can arise. The `UpdateException` will use the following codes.

|Code|Meaning|
|----|-------|
|`0`|Unknown or unexpected error.|
|`1`|Payload read failed.|
|`2`|Checksum (MD5) verification failed.|
|`3`|Backup creation failed.|
|`4`|File operation failed due to permissions.|
|`5`|Directory creation failed.|
|`6`|Chmod operation failed.|
|`7`|Executed script threw an error.|
|`8`|Executed script returned a wrong value.|
|`9`|Executed script not found.|
|`10`|Invalid update manifest.|
|`11`|Unexpected HTTP error.|
|`12`|Access denied (HTTP 403).|

## Methods

The `UpdateException` provides the following methods.

|Method|Description|
|------|-----------|
|`getResponse()`|Returns the raw response as a string for HTTP errors.|
|`getHttpCode()`|Returns the response code of the associated request.|

## Logs

Retrieves the full update log as a string with newline breaks.

```php
$log = $client->getLog();
```

-->