# Overview

This is a template for a project to develop PHP on Docker using Remote Container
feature of Visual Studio Code.

### Install libraries

For development:

```bash
$ composer install
```

For production:

```bash
$ composer install --no-dev
```

## Customize

Customize it for your project.

### Docker

#### Container name

Change the service names such as `php-docker-template-php` and 
`php-docker-template-nginx` defined in the following `docker-compose` files.

* [docker-compose.yml](docker-compose.yml)
* [.devcontainer/devcontainer.extend.yml](.devcontainer/devcontainer.extend.yml)

**NOTE:** Since `php-docker-template` is used as a prefix, rename the network
name and volume name as well.

Don't forget to change the service name of `docker-compose` in
[.devcontainer/devcontainer.json](.devcontainer/devcontainer.json).

```json5
{
    "service": "php-docker-template-php"
            //  ^^^^^^^^^^^^^^^^^^^^^^^^
}
```

##### Module version

The version of `php` and `nginx` can be changed, so change the version by
`build.args` in `docker-compose.yml` or `ARG` in `Dockerfile`.

### Visual Studio Code

#### Remote development settings

Update [.devcontainer/devcontainer.json](.devcontainer/devcontainer.json).

#### Debug

Update `pathMappings` of the debug configuration
`{"type": "php", "request": "launch"}` in
[`.vscode/launch.json`](.vscode/launch.json).

**NOTE:** This debug configuration is provided by
[PHP Debug](https://marketplace.visualstudio.com/items?itemName=felixfbecker.php-debug).

```json5
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for Xdebug",
            "type": "php",
            "request": "launch",
            "port": 9003,
            "pathMappings": {
                // Describe the path mapping between the remote
                // (Docker container) path and the local source code.
                // Update it when the remote document root is changed, or when
                // the local source code root is changed.
                // If the path mapping is wrong, Xdebug will not hit breakpoint.
                "/var/www/html": "${workspaceFolder}/src"
            }
        }
    ]
}
```

### PHP

### Composer

Check and update [composer.json](composer.json).

#### PHP_CodeSniffer

Check [.phpcs.xml](./.phpcs.xml) and set the coding conventions.

#### Xdebug

Set `xdebug.start_with_request = trigger` in
[docker/php/xdebug.ini](docker/php/xdebug.ini).
Visual Studio Code extension
[PHP Debug](https://marketplace.visualstudio.com/items?itemName=felixfbecker.php-debug)
works with this setting.

**NOTE:**  If set `xdebug.start_with_request = yes`, there will be a small delay
because `xdebug` will make a connection every time `php` command is executed.

If other debugging extensions are to be used, or if `xdebug` does not work,
change the settings.

##### `php.ini`

[docker/php/php.ini-development](docker/php/php.ini-development) and 
[docker/php/php.ini-production](docker/php/php.ini-production) are copied from
Docker container `php-docker-template-php`.

**NOTE:** `docker/php/php.ini-production` is configured in
[docker-compose.yml](docker-compose.yml) to mount. However, this will override
`docker/php/php.ini-development` in the
[.devcontainer/devcontainer.extend.yml](.devcontainer/devcontainer.extend.yml)
configuration. This configuration assumes that you use
`docker/php/php.ini-development` for debugging when using  Visual Studio Code
Remote Development feature, and use the `docker/php/php.ini-production` when
testing with `docker-compose up -d` command.

If you change the PHP version, don't forget to copy it from the Docker container
and update the configuration.

```bash
# Copy the `php.ini` file after creating the container for the new PHP version.
$ docker cp <CONTAINER_ID>:/usr/local/etc/php/php.ini-development ./docker/php/
```

## How To

#### Develop an external project

Docker container for PHP development can be used for development work on
external projects by mounting it on a Docker volume.

For this purpose, create a `.devcontainer/devcontainer.extend.private.yml` file
that is not managed by Git (or another SCM).

**NOTE:** Don't forget to include the procedure for creating this file in the
README file.

Update [.gitignore](.gitignore) to ignore this file.

`.gitignore`:

```diff
+ .devcontainer/devcontainer.extend.private.yml
```

The `.devcontainer/devcontainer.extend.private.yml` file contains settings for
mounting external projects.

Example `.devcontainer/devcontainer.extend.private.yml`:

```yaml
version: "3.8"

services:
  php-docker-template:
    volumes:
        # Mount external project from `/path/to/external/project` to `/workspace/src/`.
      - type: bind
        source: /workspace/src/
        target: /path/to/external/project
```

Update [`.devcontainer/devcontainer.json`](`.devcontainer/devcontainer.json`) to
load `.devcontainer/devcontainer.extend.private.yml` as the last Remote Container
configuration.

`.devcontainer/devcontainer.json`:

```diff
     "dockerComposeFile": [
         "../docker-compose.yml",
-        "devcontainer.extend.yml"
+        "devcontainer.extend.yml",
+        "devcontainer.extend.private.yml",
     ],
     "service": "php-docker-template-php",
```

Update [`.devcontainer/devcontainer.json`](`.devcontainer/devcontainer.json`) if
you want to use the PHP tools of an external project.

Example:

```diff
    "settings": {
-         "phpSniffer.executablesFolder": "./vendor/bin",
-         "phpSniffer.standard": "./.phpcs.xml",
-         "phpunit.php": "php",
-         "phpunit.phpunit": "${workspaceFolder}/vendor/bin/phpunit"
+         "phpSniffer.executablesFolder": "./src/laravel/vendor/bin",
+         "phpSniffer.standard": "./src/laravel/.phpcs.xml",
+         "phpunit.php": "php",
+         "phpunit.phpunit": "./src/laravel/vendor/bin/phpunit"
    }
```
