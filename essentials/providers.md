# Introduction

Horizon uses a concept called providers for some internal components which load resources. Through providers,
applications can customize how these resources are loaded.

---

# Limitations

It is important to note the following truths about providers.

- They can use the database library to run or build queries.

---

# Configuration

The provider configuration file is at `app/config/providers.php`. This is the default configuration.
[Extensions](extensions.md#providers) can also register providers.

```php
return array(
    'views' => array(
        'Horizon\View\ViewServiceProvider'
    ),
    'routing' => array(
        'Horizon\Routing\RoutingServiceProvider'
    )
);
```

To customize the providers, add the full name of your provider class to the appropriate array. You may remove the existing entries
if your provider is a total replacement.

---

# Custom Providers

## Views

Here's an example provider for loading views from a custom directory. A view provider is expected to return the string
contents of a matching view, or null if not found.

```php
namespace App\Framework\Providers;

use Horizon\Provider\ServiceProvider;

class CustomViewProvider extends ServiceProvider
{
    public function __invoke($viewName)
    {
        $path = __DIR__ . "/templates/$viewName.twig";

        if (file_exists($path)) {
            return file_get_contents($path);
        }
    }
}
```

---

## Routes

Here's an example provider for loading a custom route file. A route provider is expected to return an array of strings
containing file paths.

```php
namespace App\Framework\Providers;

use Horizon\Provider\ServiceProvider;

class CustomRouteProvider extends ServiceProvider
{
    public function __invoke()
    {
        $path = __DIR__ . "/routes/web.twig";

        if (file_exists($path)) {
            return array($path);
        }
    }
}
```

**Info:**
Remember that route files can include other route files in the same directory, so only return a single primary file per directory.

---

## Updates

Update providers are expected to return an array of Repository instances.

**Examples:**
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
