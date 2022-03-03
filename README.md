# ZLibraryBuilder

A Node.js module that makes building [BetterDiscord](https://betterdiscord.app/) plugins based
off [rauenzi's ZLibrary](https://github.com/rauenzi/BDPluginLibrary) easy!

## abstract

[BDPluginLibrary AKA ZLibrary AKA Zere's Plugin Library AKA 0PluginLibrary](https://github.com/rauenzi/BDPluginLibrary)
is an extremely common tool for creating plugins for BetterDiscord, making life so much easier. However, the process
to "build" a plugin is annoying to say the least. This project breaks away just the build scripts and adapts them to
work neatly as a CLI node module!

## install

[zlibrarybuilder is available on npm](https://www.npmjs.com/package/zlibrarybuilder)

```shell
npm i zlibrarybuilder --save-dev
```

or you can install it from the [github repo](https://github.com/HexCodeFFF/ZLibraryBuilder)

```shell
npm i https://github.com/HexCodeFFF/ZLibraryBuilder --save-dev
```

## example usage

### default build

```shell
zlibrarybuilder
```

builds the *single* plugin contained inside the `plugins` folder, outputs to the `release` folder

### build one plugin

```shell
zlibrarybuilder --pluginFolder ./plugin -ci
```

builds the plugin contained inside the `plugin` folder, adds the install script (`-i`) and copies it to
BetterDiscord (`-c`)

### backwards compatibility/build multiple plugins

```shell
zlibrarybuilder --pluginFolder ./plugins -cim
```

the `-m` argument makes it build all plugins inside the folder, like the default ZLibrary `build.js` script does.

### JSON

`package.json`

```json5
{
  "name": "myplugin",
  "dependencies": {
    "zlibrarybuilder": "^1.0.0",
    // ... (other dependencies)
  },
  // ... (other package.json stuff)
  "defaultConfig": {
    "pluginFolder": "./plugin",
    "copyToBD": true,
    "addInstallScript": true
  }
}
```

```shell
zlibrarybuilder
```

this example has the same end behavior as [the "build one plugin" example above](#build-one-plugin)

## config

### how

there are several ways to specify config options to enable both ease of use and backwards compatibility. all options are
the same, just specified differently. options are found in this order, first ones take priority:

- `config.json` file at the root of your project
- `defaultConfig` option in your `package.json`
- options specified via CLI
- default options

### options

options are listed via their CLI usage, use the **long name** as a key in either JSON file to specify it from there.

#### `-p, --pluginFolder <folder>`

default: `./plugins`

Absolute or relative path to the folder containing the plugin(s) that build scripts can build automatically.

#### `-r, --releaseFolder <folder>`

default: `./release`

Absolute or relative path to the folder where plugins that are built should be placed.

#### `-c, --copyToBD`

default: `false`

Boolean to determine if the built plugin should also be automatically copied over to your BD plugins directory. Very
convenient for development.

#### `-i, --addInstallScript`

default: `false`

Boolean to determine if the plugin should include the Windows Host Script install script. This means that users on
Windows that double click the plugin will be prompted to automatically install the plugin.

#### `-p, --packLib`

default: `false`

Boolean to include all lib functions into the plugin file.

#### `-m, --multiPlugin`

default: `false`

Boolean to re-enable the default behavior of ZLibrary, which is trying to build all plugins in the pluginFolder. If
disabled, will assume pluginFolder is the path to one plugin.

#### `-h, --help`

display help for command. won't do anything if specified via JSON.
