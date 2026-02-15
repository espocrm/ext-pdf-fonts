# Font pack for PDF in EspoCRM

Compatible with the Dompdf PDF engine. Expands support for international character sets.
Compatible The with EspoCRM v9.4 and greater.

The pack includes the general Noto Sans font and Noto Sans fonts for the following scripts:

- Japanese
- Korean
- Simplified Chinese
- Thai

<!-- separator -->

- Bengali
- Devanagari
- Gujarati
- Gurmukhi
- Kannada
- Malayalam
- Sinhala
- Tamil
- Telugu
- Oriya

<!-- separator -->

- Arabic
- Armenian
- Georgian
- Hebrew

## Installation and usage

1. Download the extension package from the [releases](https://github.com/espocrm/ext-pdf-fonts/releases).
2. Install in EspoCRM at Administration > Extensions.
3. Choose the needed font in a PDF template.


In a PDF template, you can use multiple fonts by applying the *font-family* CSS property inline. In the example below,
the first paragraph will be printed only if the *Noto Sans Tamil* font is set for the template. The second paragraph will be printed
regardless of the template's font.

```html
<p>தமிழ்</p>
<p style="font-family: 'Noto Sans Malayalam';">മലയാളം</p>
```

This pack includes the following font families:

- Noto Sans
- Noto Sans Arabic
- Noto Sans Armenian
- Noto Sans Bengali
- Noto Sans Devanagari
- Noto Sans Georgian
- Noto Sans Gujarati
- Noto Sans Gurmukhi
- Noto Sans Hebrew
- Noto Sans Japanese
- Noto Sans Kannada
- Noto Sans Korean
- Noto Sans Malayalam
- Noto Sans Oriya
- Noto Sans Simplified Chinese
- Noto Sans Sinhala
- Noto Sans Tamil
- Noto Sans Telugu
- Noto Sans Thai

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

### Extension package building

Command:

```
npm run extension
```

The package will be created in `build` directory.

Note: The version number is taken from `package.json`.

## Development workflow

1. Do development in `src` dir.
2. Run `npm run sync`.
3. Test changes in EspoCRM instance at `site` dir.

## Versioning

The version number is stored in `package.json` and `package-lock.json`.

Bumping version:

```
npm version patch
npm version minor
npm version major
```

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
