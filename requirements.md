# Requirements

## PHP

You'll need at least PHP 5.4 to use this framework. Older versions will not work correctly and will not be supported.
Running an accelerator like `opcache` is supported and recommended in production.

## Extensions

The database library has drivers which support any of the following three extensions. Only one of these extensions
must be installed, and the framework will pick the best one automatically.

- mysql
- mysqli
- pdo_mysql

The following additional extensions are recommended, but not required:

- curl
- mbstring
- openssl

## Webserver

The framework will run on any webserver that can run PHP with the above requirements. Ideally, you'll want to have
rewrite rules enabled. However, this is not a requirement. To support servers that do not have rewrite rules, see the
documentation on [fallback routing](essentials/routing.md#fallback-routing).

## Filesystem

You can install the Horizon Framework in any public-facing directory on your server, including subdirectories.

The framework was built to support shared hosting environments where subdirectory installations may be necessary. For this
reason, it will automatically detect if it is installed in a subdirectory, and will prefix all routes as necessary.

It is recommended to have at least **50 MiB** of available disk space in the environment where you install the framework.
While much of this may be unused currently, future framework updates can and will increase the disk space needs.
