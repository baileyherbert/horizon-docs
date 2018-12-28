<!-- NO_TOC -->

# Introduction

## Welcome to Horizon

Horizon is a framework for building distributable web and console applications in PHP. It is heavily inspired by
Laravel and many of its components have a matching or very similar interface. So what makes it special?

### It's highly compatible

Laravel and other big frameworks all have very specific server requirements, and they aren't always suitable for use
with shared hosting or other circumstances such as being placed in subdirectories. When working with web applications
that will be sold, it's imperative that a wide range of servers are supported. Horizon fulfills that purpose:

- It has built-in drivers for `mysql`, `mysqli`, `mysqlnd`, and `pdo_mysql`.
- It natively supports as low as PHP 5.4.
- It works seamlessly in subdirectories.
- It has fallback routing for servers without `mod_rewrite`.

### It's extensible

That's right. There is a built-in extension system which can make it very easy to implement common feats like themes,
plugins, or other types of add-ons into your application. Extensions can have their own source code, their own
namespaces, even their own Composer packages.

### It's featureful

From a built-in, ready-to-go automatic update system to the most powerful and developed open-source query builder in the
PHP world, Horizon has at least something that will make your life easier.
