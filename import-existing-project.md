---
description: Import an existing Electron project to use Electron Forge.
---

# Importing an Existing Project

If you already have an Electron project and want to try out Electron Forge, you can either use Forge's import script or manually install Forge yourself.

These steps will get you set up with a basic build pipeline that can create [squirrel.windows.md](config/makers/squirrel.windows.md "mention") (Windows), [zip.md](config/makers/zip.md "mention") (macOS), and [deb.md](config/makers/deb.md "mention") (Linux) installers when running `electron-forge make`.

## Using the import script

Importing an existing Electron app into the Electron Forge workflow can be done automatically using Forge's `import` command.

{% tabs %}
{% tab title="Yarn 1" %}
```shell
cd my-app
yarn add --dev @electron-forge/cli
yarn electron-forge import
```
{% endtab %}

{% tab title="npm 6" %}
```bash
cd my-app
npm install --save-dev @electron-forge/cli
npx electron-forge import
```
{% endtab %}

{% tab title="npm 7" %}
```shell
cd my-app
npm install --save-dev @electron-forge/cli
npm exec --package=@electron-forge/cli -c "electron-forge import"
```
{% endtab %}
{% endtabs %}

This script will set up Forge to package and&#x20;

{% hint style="info" %}
If you're already using other Electron tooling, it will try to automatically migrate the settings as much as possible, but some of it may need to be migrated manually.
{% endhint %}

## Setting up Forge manually

If the import script does not work for some reason, you can also install Forge manually. To get identical behavior to the script, follow the steps below.

### Installing dependencies

First, install Forge's CLI and the target Makers as devDependencies in your project.

{% tabs %}
{% tab title="Yarn 1" %}
```shell
cd my-app
yarn add --dev @electron-forge/cli @electron-forge/maker-squirrel @electron-forge/maker-deb @electron-forge/maker-zip
```
{% endtab %}

{% tab title="npm" %}
```bash
cd my-app
npm install --save-dev @electron-forge/cli @electron-forge/maker-squirrel @electron-forge/maker-deb @electron-forge/maker-zip
```
{% endtab %}
{% endtabs %}

### Configuring package.json

To start using Forge, add a few command scripts to your package.json file:

{% code title="package.json" %}
```json
{
  // ...
  "scripts": {
    "start": "electron-forge start",
    "package": "electron-forge package",
    "make": "electron-forge make"
  }
  // ... 
}
```
{% endcode %}

Then, set up your Forge [configuration.md](config/configuration.md "mention") in the `config.forge` field in package.json.

{% code title="package.json" %}
```json
{
  // ...
  "config": {
    "forge": {
      "packagerConfig": {},
      "makers": [
        {
          "name": "@electron-forge/maker-squirrel",
          "config": {
            "name": "electron_quick_start"
          }
        },
        {
          "name": "@electron-forge/maker-zip",
          "platforms": [
            "darwin"
          ]
        },
        {
          "name": "@electron-forge/maker-deb",
          "config": {}
        },
        {
          "name": "@electron-forge/maker-rpm",
          "config": {}
        }
      ]
    }
  }
  // ...
}
```
{% endcode %}

In the above object, we configure each Maker that we installed into the `makers` array. We also create an empty `packagerConfig` object that you should edit to your app's needs.

### Adding Squirrel.Windows boilerplate

When distributing a Squirrel.Windows app, we recommend installing [`electron-squirrel-startup`](https://github.com/mongodb-js/electron-squirrel-startup) as a runtime dependency to handle Squirrel events.

{% tabs %}
{% tab title="Yarn" %}
```shell
cd my-app
yarn add electron-squirrel-startup
```
{% endtab %}

{% tab title="npm" %}
```bash
cd my-app
npm install electron-squirrel-startup
```
{% endtab %}
{% endtabs %}

Then, add the following snippet as early as possible in the main process execution (before the `app.ready` event).

{% code title="main.js" %}
```javascript
if (require('electron-squirrel-startup')) app.quit();
```
{% endcode %}
