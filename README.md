# ZLibraryBuilder

A Node.js module that makes building [BetterDiscord](https://betterdiscord.app/) plugins based
off [rauenzi's ZLibrary](https://github.com/rauenzi/BDPluginLibrary) easy!

## abstract

[BDPluginLibrary AKA ZLibrary AKA Zere's Plugin Library AKA 0PluginLibrary](https://github.com/rauenzi/BDPluginLibrary)
is an extremely common tool for creating plugins for BetterDiscord, making life so much easier. However, the process
to "build" a plugin is annoying to say the least. This project breaks away just the build scripts and adapts them to
work neatly as a CLI node module!

## improvements over BDPluginLibrary's build.js

- the main purpose of the module: able to be called and configured externally
- [able to build just one plugin without embedding in a plugin folder](#build-one-plugin)
- [dynamically embed external node modules into your plugin](#dynamic-node-module-embed)

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

builds the plugin contained inside the `plugin` folder, adds the install script ([`-i`](#-i---addinstallscript)) and
copies it to BetterDiscord ([`-c`](#-c---copytobd))

### backwards compatibility/build multiple plugins

```shell
zlibrarybuilder --pluginFolder ./plugins -cimo
```

the [`-m`](#-m---multiplugin) argument makes it build all plugins inside the folder, like the default
ZLibrary `build.js` script does.

the [`-o`](#-o---oldheader) argument is for backwards compatability, as this library changes some compilation behavior.

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
  "buildConfig": {
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

- options specified via CLI
- `buildConfig` field in your `package.json`
- `config.json` file at the root of your project
- `defaultConfig` field in your `package.json`
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

#### `-o, --oldHeader`

default: `false`

Boolean to re-enable the normal ZLibrary plugin header generation. The existing generation was strange and limited, and
this option only exists for backwards compatability. Leave disabled for the improved behavior, passing all
plugin `config.info` keys as JSDoc entries.
[View the official BetterDiscord docs for more info about these.](https://github.com/BetterDiscord/BetterDiscord/wiki/Plugin-and-Theme-METAs#common-fields)

keep in mind that the ZLibrary `authors` field overwrites the `author` and `authorId` properties. ZLibraryBuilder (when
oldHeader is off) attempts to convert these into the correct ZLibrary config options if `authors` is unspecified, but it
is better to just work with ZLibrary.

#### `-h, --help`

display help for command. won't do anything if specified via JSON.

## dynamic node module embed

ZLibraryBuilder has the ability to automatically compile and embed external node.js modules into your plugin for you in
2 easy steps

1. make sure the module in question is installed in your node_modules
2. add a comment so ZLibraryBuilder knows to replace the `require()`

```js
// this statement will remain unmodified
require('ts-leaky-bucket');
// zlibrarybuilder will replace this with the entire library code for you!
require('ts-leaky-bucket' /* zlibrarybuilder embed */);
```

the reason that not all require calls are replaced by default is that discord itself is a node app and it is highly
recommended to properly `require()` from discord's modules when possible

**note:** ZLibraryBuilder naively replaces all (properly commented) `require()` calls with library embeds. if you
require the module twice, it'll be in your plugin twice. please require it once to a variable and reuse the variable.
