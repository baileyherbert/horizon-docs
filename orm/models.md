# Models

## Introduction

Horizon's model instances represent a row of data from your database. They work seamlessly with the query builder.

Due to their intelligent caching and powerful interface, you should use models where possible, rather than
manually fetching arrays of query rows.

### Example

This is a basic example of a model.

```php
namespace App\Models;

use Horizon\Database\Model;
use Horizon\Database\ORM\Relationships\OneToManyRelationship;
use Horizon\Database\ORM\Relationships\ManyToManyRelationship;

class User extends Model
{

    /**
     * @return OneToManyRelationship
     */
    public function posts()
    {
        return $this->hasMany('App\Models\Post');
    }

    /**
     * @return ManyToManyRelationship
     */
    public function roles()
    {
        return $this->belongsToMany('App\Models\Role', 'user_roles');
    }

}
```

## Mapping

Horizon automatically attempts to map a model to a table by taking the plural form of its name. For example, a model
called `User` will be matched to a table called `users`.

Furthermore, models always assume the primary key is called `id` by default.

You can customize this mapping by overwriting the model's `$table` and `$primaryKey` properties.

```php
class User extends Model
{
    protected $table = 'forum_users';
    protected $primaryKey = 'user_id';
}
```

## Retrieving instances

Models have some static methods which are used to generate instances from the database.

### Find by primary key

Find a model instance by its primary key. Returns `null` if not found.

```php
$user = App\Models\User::find(2);
```

You can also generate an HTTP error if it isn't found using `findOrFail`. The second parameter is the error code and
defaults to `404`.

```php
$user = App\Models\User::findOrFail(2);
```

### Find by query building

Easily search for models using query building. This will return an array of found models. See also the
[available methods](../database/query_builder.md#select) for `WHERE` queries.

```php
$users = App\Models\User::where('username', 'like', 'john.doe')
    ->orWhere('username', 'like', 'jane.doe')
    ->get();
```

### Get all rows

Get an array of model instances for all rows in the table. Be careful with this!

```php
$users = App\Models\User::all();
```

## Properties

Once you have a model instance, you can access its column values as properties.

```php
$id = $user->id;
$userName = $user->username;
```

## Inserting & Updating

Creating a new model instance is the same as creating any other object.

```php
$user = new App\Models\User();
```

On any model instance, you may set its column values like properties.

```php
$user->username = 'john.doe';
$user->email = 'john.doe@example.com';
$user->last_online = null;
```

These changes, however, are not applied to the database until you call the `save` method, which will update or insert
the row.

```php
$user->save();
```

After you call `save` on a new instance, the primary key is automatically updated.

```php
$newUsersId = $user->id;
```

### Lazy Insertion

An easier way to create new rows and get the resulting model instance is the `create` static method.

```php
$user = App\Models\User::create(array(
    'username' => 'john.doe',
    'email' => 'john.doe@example.com',
    'last_online' => null
));
```

## Deleting

To delete an individual model instance, call the `delete` method. It returns an array consisting of the model's data
before deletion. The model instance will be wiped immediately.

```php
$properties = $user->delete();
$id = $properties['id'];
```
