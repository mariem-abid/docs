---
menuTitle: Composer
title: External dependencies & autoload with Composer
description: Learn how to migrate your current shop (1.6 or previous version) to PrestaShop 1.7

weight: 7
---

# Composer

Composer is a tool known in the PHP ecosystem to manage the dependencies of a project.
It has been originally added in PrestaShop 1.7 to handle the inclusion of the Symfony framework and the native modules.
As composer is compatible with PHP 5.3, it can be used in a module even compatible with previous major versions of PrestaShop.

From a single configuration file, a module will benefit of these features:

* Autoload of your classes, namespaced or not
* Check the environment complies with the requirements 
* Download of external PHP dependencies

References:

* [Composer homepage](https://getcomposer.org)
* [Documentation](https://getcomposer.org/doc/)
* [Learn more about autoload](https://www.php.net/manual/en/language.oop5.autoload.php)

## Install composer

If Composer is not installed in you environment, this can be done via their website [getcomposer.org](https://getcomposer.org/).
We recommend to store the binary in a folder available in your system or user path, to avoid looking for it at each command.

## Setup composer in a module

You need setup composer in your module before create the services.
Create the file `<module name>/composer.json` and paste:
```json
{
    "name": "<your name>/<module name>",
    "description": "<module description>",
    "authors": [
        {
            "name": "<your name>",
            "email": "<your email>"
        }
    ],
    "require": {
        "php": ">=5.6.0"
    },
    "autoload": {
        "psr-4": {
            "<YourNamespace>\\": "src/"
        },
        "classmap": [
            "<file>.php",
            "classes/"
        ],
        "exclude-from-classmap": []
    },
    "config": {
        "preferred-install": "dist",
        "prepend-autoloader": false
    },
    "type": "prestashop-module",
    "author": "<???>",
    "license": "<???>"
}
```

### Autoloading

#### Classes

To make a class automatically loaded in memory as soon as you reference it in your code,
you should add in the list `autoload.classmap` the file it is stored in.

#### Namespaced classes

The classes defined in a namespace can be detected if you ask Composer to scan a folder in `autoload.psr-4`.

In __YourNamespace__ add your namespace. The convention for namespaces used by PrestaShop is
`PrestaShop\\Module\\ModuleName` as our base namespace, you can either follow this convention or adapt it to your
business `YourCompany\\YourModuleName`.

#### Generate the autoloader

Then in console in your module root run command `composer dump-autoload`.
This will generate a **vendor** folder contain an **autoload.php** file which allows the use of your namespace or
the classes defined in the classmap.

You can also use composer to include some dependencies in your module, you can find more information about composer
on [Composer page](https://getcomposer.org/).

### Important notes

{{% notice warning %}}

**Disable prepend-autoloader**

It is **required** for you to disable the *prepend autoloader* feature. By default the module dependencies would be
defined before the core ones, which would result in overriding them. If you want the PrestaShop core system to work
correctly you **must not** override its dependencies. Which is why you need to always add in your composer.json file:

```yaml
    "config": {
        "prepend-autoloader": false
    }
```
{{% /notice %}}

{{% notice warning %}}

**Don't forget to publish your vendor folder**

When using composer the autoload file generated by composer is required for your module to work correctly. So before
creating your module archive and releasing it don't forget to run `composer dump-autoload -o` (or `composer install --no-dev` if
you have included production dependencies) so that they will be included in your module.
{{% /notice %}}