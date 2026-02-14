# Font pack for PDF in EspoCRM

Compatible with the Dompdf PDF engine. Expands support for international character sets.

The pack includes the general Noto Sans font and Noto Sans fonts for the following scripts:

- Japanese
- Korean
- Simplified Chinese

<!-- separator -->

- Tamil
- Telugu
- Malayalam
- Kannada

<!-- separator -->

- Devanagari
- Bengali
- Gujarati
- Oriya
- Gurmukhi

<!-- separator -->

- Arabic


## Installation and usage

1. Download the extension package from releases.
2. Install in EspoCRM at Administration > Extensions.
3. Choose the needed font in a PDF template.

---

Everything below is for developers.

## Configuration

Create `config.json` file in the root directory. You can copy `config-default.json` and rename it to `config.json`.

When reading, this config will be merged with `config-default.json`. You can override default parameters in the created config.

Parameters:

* espocrm.repository – from what repository to fetch EspoCRM;
* espocrm.branch – what branch to fetch (`stable` is set by default); you can specify version number instead (e.g. `9.1.0`);
* database - credentials of the dev database;
* install.siteUrl – site url of the dev instance;
* install.defaultOwner – a webserver owner (important to be set right);
* install.defaultGroup – a webserver group (important to be set right).


## Config for EspoCRM instance

You can override EspoCRM config. Create `config.php` in the root directory of the repository. This file will be applied after EspoCRM installation (when building).

Example:

```php
<?php
return [
    'useCacheInDeveloperMode' => true,
];
```

## Building

After building, EspoCRM instance with installed extension will be available at `site` directory. You will be able to access it with credentials:

* Username: admin
* Password: 1

### Preparation

1. You need to have *node*, *npm*, *composer* installed.
2. Run `npm install`.
3. Create a database. Note that without the created database instance building will fail. The database name is set in the config file. You can change it.

### Full EspoCRM instance building

It will download EspoCRM (from the repository specified in the config), then build and install it. Then it will install the extension.

Command:

```
npm run all
```

Note: It will remove a previously installed EspoCRM instance, but keep the database intact.

Note: If an error occurred, check `site/data/logs/` for details. It's often a database is not created.

### Copying extension files to EspoCRM instance

You need to run this command every time you make changes in `src` directory, and you want to try these changes on Espo instance.

Command:

```
npm run sync
```

To avoid running this command manually, use a file watcher in your IDE. The configuration for PhpStorm is included in this repository. See below about the file watcher.

### Running after-install script

AfterInstall.php will be applied for EspoCRM instance.

Command:

```
node build --after-install
```

### Extension package building

Command:

```
npm run extension
```

The package will be created in `build` directory.

Note: The version number is taken from `package.json`.

### Installing addition extensions

If your extension requires other extensions, there is a way to install them automatically while building the instance.

Necessary steps:

1. Add the current EspoCRM version to the `config.php`:

```php
<?php
return [
    'version' => '9.0.0',
];

```

2. Create the `extensions` directory in the root directory of your repository.
3. Put needed extensions (e.g. `my-extension-1.0.0.zip`) in this directory.

Extensions will be installed automatically after running the command `node build --all` or `node build --install`.

## Development workflow

1. Do development in `src` dir.
2. Run `npm run sync`.
3. Test changes in EspoCRM instance at `site` dir.


## Using composer in extension

If your extension requires additional libraries, they can be installed by composer:

1. Create a file `src/files/custom/Espo/Modules/{ModuleName}/composer.json` with your dependencies.
2. Once you run `node build --all` or `node build --composer-install`, composer dependencies will be automatically installed.
3. Create a file `src/files/custom/Espo/Modules/{ModuleName}/Resources/autoload.json`.

Note: The extension build will contain only the `vendor` directory without the `composer.json` file.

The `autoload.json` file defines paths for namespaces:

```json
{
    "psr-4": {
        "LibraryNamespace\\": "custom/Espo/Modules/{ModuleName}/vendor/<vendor-name>/<library-name>/path/to/src"
    }
}
```

## Versioning

The version number is stored in `package.json` and `package-lock.json`.

Bumping version:

```
npm version patch
npm version minor
npm version major
```

## Tests

To prepare the Espo instance:

```
npm run prepare-test
```

Fetches the instance and runs composer install. To be used for unit tests and static analysis in CI environment. Takes less time than the full installation.


### Unit

Run composer install for the site:

```
(cd site; composer install)
```

Command to run unit tests:

```
(npm run sync; cd site; vendor/bin/phpunit tests/unit/Espo/Modules/PdfFonts)
```

or

```
npm run unit-tests
```

### Integration

You need to build a test instance first:

1. `npm run sync`
2. `(cd site; grunt test)`

You need to create a config file `tests/integration/config.php`:

```php
<?php

return [
    'database' => [
        'driver' => 'pdo_mysql',
        'host' => 'localhost',
        'charset' => 'utf8mb4',
        'dbname' => 'TEST_DB_NAME',
        'user' => 'YOUR_DB_USER',
        'password' => 'YOUR_DB_PASSWORD',
    ],
];
```

Command to run integration tests:

```
(npm run sync; cd site; vendor/bin/phpunit tests/integration/Espo/Modules/PdfFonts)
```

or

```
npm run integration-tests
```

Note that integration tests needs the full Espo installation.

### Static analysis

Command to run:

```
npm run sync; site/vendor/bin/phpstan
```

or

```
npm run sa
```

If your extension contains additional PHP packages, you also need to add `site/custom/Espo/Modules/PdfFonts/vendor` to the *scanDirectories* section in *phpstan.neon* config.

Note: You can omit *composer-install* command if your extension does not contain PHP packages.

## Configuring IDE

You need to set the following paths to be ignored in your IDE:

* `build`
* `site/build`
* `site/custom/`
* `site/client/custom/`
* `site/tests/unit/Espo/Modules/PdfFonts`
* `site/tests/integration/Espo/Modules/PdfFonts`

### File watcher

You can set up a file watcher in the IDE to automatically copy and transpile files upon saving.

File watcher parameters for PhpStorm:

* Program: `node`
* Arguments: `build --copy-file --file=$FilePathRelativeToProjectRoot$`
* Working Directory: `$ProjectFileDir$`

Note: The File Watcher configuration for PhpStorm is included in this repository.

## Using ES modules

The initialization script asks whether you want to use ES6 modules. It's recommended to choose "YES".

If you have chosen No and want to switch to ES6 later, then:

1. Set *bundled* to true in `extension.json`.
2. Set *bundled* and *jsTranspiled* to true in `src/files/custom/Espo/Modules/PdfFonts/Resources/module.json`.
3. Add `src/files/custom/Espo/Modules/PdfFonts/Resources/metadata/app/client.json`
    ```json
    {
        "scriptList": [
            "__APPEND__",
            "client/custom/modules/pdf-fonts/lib/init.js"
        ]
    }
    ```

## Javascript frontend libraries

Install *rollup*.

In `extension.json`, add a command that will bundle the needed library into an AMD module. Example:

```json
{
    "scripts": [
        "npx rollup node_modules/some-lib/build/esm/index.mjs --format amd --file build/assets/lib/some-lib.js --amd.id some-lib"
    ]
}
```

Add the library module path to `src/files/custom/Espo/Modules/PdfFonts/Resources/metadata/app/jsLibs.json`

```json
{
    "some-lib": {
        "path": "client/custom/modules/pdf-fonts/lib/some-lib.js"
    }
}
```

When you build, the library module will be automatically included in the needed location.

Note that you may also need to create *rollup.config.js* to set some additional Rollup parameters that are not supported via CLI usage.

## Updating tooling libraries

Update the version number of espo-extension-tools in package.json to the [latest one](https://github.com/espocrm/extension-tools/releases).

Run:

```
npm update espo-extension-tools
npm update espo-frontend-build-tools
```

Or just update everything:

```
npm update
```

