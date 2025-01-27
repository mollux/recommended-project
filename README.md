# Composer template for Mautic projects

This project template provides a starter kit for managing your Mautic
dependencies with [Composer](https://getcomposer.org/).

## Usage

First you need to [install composer 2](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx).

> Note: The instructions below refer to the [global composer installation](https://getcomposer.org/doc/00-intro.md#globally).
You might need to replace `composer` with `php composer.phar` (or similar)
for your setup.

After that you can create the project:

```
composer create-project mautic/recommended-project:4.x-dev some-dir --no-interaction
```

With `composer require ...` you can download new dependencies to your
installation.

```
cd some-dir
composer require mautic/mautic-saelos-bundle:~2.0
```

The `composer create-project` command passes ownership of all files to the
project that is created. You should create a new git repository, and commit
all files not excluded by the .gitignore file.

## What does the template do?

When installing the given `composer.json` some tasks are taken care of:

* Mautic will be installed in the `public`-directory.
* Autoloader is implemented to use the generated composer autoloader in `vendor/autoload.php`,
  instead of the one provided by Mautic (`public/vendor/autoload.php`).
* Plugins (packages of type `mautic-plugin`) will be placed in `public/plugins/`
* Themes (packages of type `mautic-theme`) will be placed in `public/themes/`
* Creates `public/media`-directory.
* Creates environment variables based on your .env file. See [.env.example](.env.example).

## Updating Mautic Core

This project will attempt to keep all of your Mautic Core files up-to-date; the
project [mautic/core-composer-scaffold](https://github.com/mautic/core-composer-scaffold)
is used to ensure that your scaffold files are updated every time mautic/core is
updated. If you customize any of the "scaffolding" files (commonly .htaccess),
you may need to merge conflicts if any of your modified files are updated in a
new release of Mautic core.

Follow the steps below to update your core files.

1. Run `composer update mautic/core --with-dependencies` to update Mautic Core and its dependencies.
2. Run `git diff` to determine if any of the scaffolding files have changed.
   Review the files for any changes and restore any customizations to
  `.htaccess` or others.
1. Commit everything all together in a single commit, so `public` will remain in
   sync with the `core` when checking out branches or running `git bisect`.
1. In the event that there are non-trivial conflicts in step 2, you may wish
   to perform these steps on a branch, and use `git merge` to combine the
   updated core files with your customized files. This facilitates the use
   of a [three-way merge tool such as kdiff3](http://www.gitshah.com/2010/12/how-to-setup-kdiff-as-diff-tool-for-git.html). This setup is not necessary if your changes are simple;
   keeping all of your modifications at the beginning or end of the file is a
   good strategy to keep merges easy.

## FAQ

### Should I commit the contributed plugins I download?

Composer recommends **no**. They provide [argumentation against but also
workrounds if a project decides to do it anyway](https://getcomposer.org/doc/faqs/should-i-commit-the-dependencies-in-my-vendor-directory.md).

### Should I commit the scaffolding files?

The [Mautic Composer Scaffold](https://github.com/mautic/core-composer-scaffold) plugin can download the scaffold files (like
index.php, .htaccess, …) to the public/ directory of your project. If you have not customized those files you could choose
to not check them into your version control system (e.g. git). If that is the case for your project it might be
convenient to automatically run the mautic-scaffold plugin after every install or update of your project. You can
achieve that by registering `@composer mautic:scaffold` as post-install and post-update command in your composer.json:

```json
"scripts": {
    "post-install-cmd": [
        "@composer mautic:scaffold",
        "..."
    ],
    "post-update-cmd": [
        "@composer mautic:scaffold",
        "..."
    ]
},
```
### How can I apply patches to downloaded plugins?

If you need to apply patches (depending on the project being modified, a pull
request is often a better solution), you can do so with the
[composer-patches](https://github.com/cweagans/composer-patches) plugin.

To add a patch to Mautic plugin foobar insert the patches section in the extra
section of composer.json:
```json
"extra": {
    "patches": {
        "mautic/foobar": {
            "Patch description": "URL or local path to patch"
        }
    }
}
```

### How do I specify a PHP version ?

This project supports PHP 7.4 as minimum version, however it's possible that a `composer update` will upgrade some package that will then require PHP 7+ or 8+.

To prevent this you can add this code to specify the PHP version you want to use in the `config` section of `composer.json`:
```json
"config": {
    "sort-packages": true,
    "platform": {
        "php": "7.4"
    }
},
```
