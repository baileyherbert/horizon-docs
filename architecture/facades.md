# Facades

## Introduction

Horizon is a new framework and its internal APIs can change very often. To support application-level development despite
these frequent changes, facades were introduced. These are helper classes with static methods that perform common tasks
on the framework level.

## Usage

All built-in facades are under the `Horizon\Support\Facades` namespace. All you need to do is import them.

```php
use Horizon\Support\Facades\Application;
$absolutePath = Application::path('/app/config/');
```

## List of Facades

|Facade|Description|
|------|-----------|
|`Application`|Facade interface for application-level methods and data.|
|`Framework`|Facade interface for framework-level methods and data.|
|`Http`|Facade interface for interacting with the Http kernel.|

**Tip:** Facades are a new feature and this list will continue to grow.
