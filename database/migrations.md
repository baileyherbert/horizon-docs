# Migrations

## Introduction

Horizon's migrations allow you to programatically create and alter tables and their schemas.

```php
use Database;
use Horizon\Database\Migration\Blueprint;
use Horizon\Database\Migration\Schema;

Database::migrate(function(Schema $schema) {
    $schema->create('users', function(Blueprint $table) {
        $table->increments('id');
        $table->string('email');
        $table->string('password');
        $table->timestamp('created_at')->useCurrent();
        $table->timestamp('online_at')->useCurrent();
    });
});
```
