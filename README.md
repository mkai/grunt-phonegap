# Grunt: Phonegap
> A [Grunt](http://gruntjs.com/) plugin to provide build tasks for [Phonegap](http://phonegap.com/) applications

[![Build Status](http://ci.ldk.io/logankoester/grunt-phonegap/badge)](http://ci.ldk.io/logankoester/grunt-phonegap/)
[![Dependency Status](https://david-dm.org/logankoester/grunt-phonegap.png)](https://david-dm.org/logankoester/grunt-phonegap)
[![devDependency Status](https://david-dm.org/logankoester/grunt-phonegap/dev-status.png)](https://david-dm.org/logankoester/grunt-phonegap#info=devDependencies)
[![Gittip](http://img.shields.io/gittip/logankoester.png)](https://www.gittip.com/logankoester/)

[![NPM](https://nodei.co/npm/grunt-phonegap.png?downloads=true)](https://nodei.co/npm/grunt-phonegap/)

`grunt-phonegap` integrates Phonegap development with [Grunt](http://gruntjs.com/)-based workflows
by wrapping the Phonegap 3.0 command line interface.

Rather than polluting the top-level of your project, `grunt-phonegap` copies your files into a
subdirectory containing the Phonegap project, which gets regenerated every time the task `phonegap:build` is executed.

## Requirements

You will need the `phonegap` CLI tool installed globally to use `grunt-phonegap`.

```shell
npm install phonegap -g
```
You should also install whatever system dependencies are required by the platforms
you intend to target.

For help with that, see [Platform Guides](http://docs.phonegap.com/en/3.1.0/guide_platforms_index.md.html#Platform%20Guides) from the Phonegap documentation.

## Getting Started
This plugin requires Grunt `~0.4.1`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install grunt-phonegap --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-phonegap');
```

### Overview
In your project's Gruntfile, add a section named `phonegap` to the data object passed into `grunt.initConfig()`.

Point `phonegap.config.root` to the output of your other build steps (where your `index.html` file is located).

Point `phonegap.config.config` to your [config.xml](http://docs.phonegap.com/en/3.0.0/config_ref_index.md.html) file.

`phonegap.config.cordova` should be the `.cordova` directory that is generated by `phonegap create`. It must
contain a `config.json` file or your app cannot be built.

### Options

```js
grunt.initConfig({
  phonegap: {
    config: {
      root: 'www',
      config: 'www/config.xml',
      cordova: '.cordova',
      path: 'phonegap',
      plugins: ['/local/path/to/plugin', 'http://example.com/path/to/plugin.git'],
      platforms: ['android'],
      maxBuffer: 200, // You may need to raise this for iOS.
      verbose: false,
      releases: 'releases',
      releaseName: function(){
        var pkg = grunt.file.readJSON('package.json');
        return(pkg.name + '-' + pkg.version);
      },

      // Add a key if you plan to use the `release:android` task
      // See http://developer.android.com/tools/publishing/app-signing.html
      key: {
        store: 'release.keystore',
        alias: 'release',
        aliasPassword: function(){
          // Prompt, read an environment variable, or just embed as a string literal
          return('');
        },
        storePassword: function(){
          // Prompt, read an environment variable, or just embed as a string literal
          return('');
        }
      },

      // Set an app icon at various sizes (optional)
      icons: {
        android: {
          ldpi: 'icon-36-ldpi.png',
          mdpi: 'icon-48-mdpi.png',
          hdpi: 'icon-72-hdpi.png',
          xhdpi: 'icon-96-xhdpi.png'
        },
        wp8: {
          app: 'icon-62-tile.png',
          tile: 'icon-173-tile.png'
        }
      },

      // Set a splash screen at various sizes (optional)
      // Only works for Android now
      screens: {
        android: {
          ldpi: 'screen-ldpi-portrait.png'
          // landscape version
          ldpiLand: 'screen-ldpi-landscape.png'
          mdpi: 'screen-mdpi-portrait.png'
          // landscape version
          mdpiLand: 'screen-mdpi-landscape.png'
          hdpi: 'screen-hdpi-portrait.png'
          // landscape version
          hdpiLand: 'screen-hdpi-landscape.png'
          xhdpi: 'screen-xhdpi-portrait.png'
          // landscape version
          xhdpiLand: 'www/screen-xhdpi-landscape.png'
        }
      },

      // Android-only integer version to increase with each release.
      // See http://developer.android.com/tools/publishing/versioning.html
      versionCode: function(){ return(1) },

      // If you want to use the Phonegap Build service to build one or more
      // of the platforms specified above, include these options.
      // See https://build.phonegap.com/
      remote: {
        username: 'your_username',
        password: 'your_password',
        platforms: ['android', 'blackberry', 'ios', 'symbian', 'webos', 'wp7']
      }
    }
  }
})
```

#### Dynamic config.xml

Beginning with **v0.4.1**, `phonegap.config.config` may be either a string or an object.

As a string, the file is copied directly, as with previous versions.

As an object with the keys `template<String>` and `data<Object>`, the file at `template` will
be processed using [grunt.template](http://gruntjs.com/api/grunt.template).

##### Example

**Gruntfile.js**


```js
  // ...
  phonegap: {
    config: {
      config: {
        template: '_myConfig.xml',
        data: {
          id: 'com.grunt-phonegap.example'
          version: grunt.pkg.version
          name: grunt.pkg.name
        }
      }
  // ...
```

**_myConfig.xml**

```xml
<?xml version='1.0' encoding='utf-8'?>
<widget
  id="<%= id %>"
  version="<%= version %>"
  xmlns="http://www.w3.org/ns/widgets"
  xmlns:gap="http://phonegap.com/ns/1.0">

    <name><%= name %></name>

    <!-- ... -->
</widget>

```
#### App Icons

If you choose to set `phonegap.config.icons` with one or more icon sizes, these files
will be copied into the appropriate directories to use as your app icon.

You may want to use this feature in conjunction with [grunt-rasterize](https://github.com/logankoester/grunt-rasterize)
to generate the correctly sized icon files from an SVG source.

Currently this feature only supports Android and Windows Phone 8.

##### Example

```js
  phonegap: {
    config: {
      // ...
      icons: {
      	android: {
            ldpi: 'icon-36-ldpi.png',
            mdpi: 'icon-48-mdpi.png',
            hdpi: 'icon-72-hdpi.png',
            xhdpi: 'icon-96-xhdpi.png'
        },
        wp8: {
        	app: 'icon-62-tile.png',
            tile: 'icon-173-tile.png'
        }
      }
      // ...
    }
  }
```

#### versionCode

The [config-xml](https://build.phonegap.com/docs/config-xml) documentation from Phonegap Build (the remote build service)
indicate that you can set a **versionCode** for your `AndroidManifest.xml` file inside your `config.xml`. However, `phonegap local`
just ignores that property.

[Google Play](http://developer.android.com/distribute/index.html) will not allow you to upload more than one APK with the same `versionCode`.

If you set a `phonegap.config.versionCode` value (function or literal), `grunt phonegap:build` will post-process the generated
`AndroidManifest.xml` file and set it for you.

In most applications it should simply be an integer that you increment with each release.

See http://developer.android.com/tools/publishing/versioning.html

This option will be ignored for non-Android platforms or when using the remote build service.

#### Phonegap Build

If you set `phonegap.config.remote` to a subset of `phonegap.config.platforms`, those platforms will be built remotely. This is still somewhat
experimental, and may not integrate with all local features.

### Tasks

#### phonegap:build[:platform]

Running `phonegap:build` with no arguments will...

* Purge your `phonegap.config.path`
* Copy your `phonegap.config.cordova` and `phonegap.config.root` files into it
* Add any plugins listed in `phonegap.config.plugins`
* ..and then generate a Phonegap build for all platforms listed in `phonegap.config.platforms`

If you pass a specific platform as an argument (eg `grunt phonegap:build:android`), the `phonegap.config.platforms` array will be
ignored and only that specific platform will be built.

#### phonegap:run[:platform][:device]

After a build is complete, the `phonegap:run` grunt task can be used to launch your app
on an emulator or hardware device. It accepts two optional arguments, `platform` and `device`.

Example: `grunt phonegap:run:android:emulator`

If you are using the Android platform, you can see the list of connected devices by running `adb devices`.

The platform argument will default to the first platform listed in `phonegap.config.platforms`.

#### phonegap:release[:platform]

Create a releases/ directory containing a signed application package for distribution.

Currently `android` is the only platform supported by this task. You will need to create
a keystore file at `phonegap.config.key.store` like this:

    $ keytool -genkey -v -keystore release.keystore -alias release -keyalg RSA -keysize 2048 -validity 10000

The keytool command will interactively ask you to set store and alias passwords, which must match
the return value of `phonegap.config.key.aliasPassword` and `phonegap.config.key.storePassword` respectively.

#### phonegap:login

Log into the Phonegap Build service with the credentials specified at `phonegap.config.remote.username`
and `phonegap.config.remote.password`.

#### phonegap:logout

Log out of the Phonegap Build service.

## Running the test suite

    git clone https://github.com/logankoester/grunt-phonegap.git
    cd grunt-phonegap
    npm install
    git submodule init
    git submodule update
    grunt

Note that not all tests can be run on all platforms. For example, tests depending on the Windows Phone SDK
will be skipped if your OS is detected to be non-Windows.

## Contributing

Fork the repo on Github and open a pull request. Note that the files in `tasks/` and `test/` are the output of
CoffeeScript files in `src/`, and will be overwritten if edited by hand.

Before running the included test suite, you must first run `git submodule update` on your local clone (see above).

## Release History

#### 0.9.0

A large amount of code has been refactored, which may have introduced new bugs. Please [open an issue](https://github.com/logankoester/grunt-phonegap/issues)
if a feature no longer works with your `v0.8.1` config.

  * Adds support for remote builds using the [Phonegap Build](https://build.phonegap.com) service ([#31](https://github.com/logankoester/grunt-phonegap/issues/31))
  * Refactors tasks source into feature-specific files ([#28](https://github.com/logankoester/grunt-phonegap/issues/28))

#### 0.8.1
  * Adds optional platform argument for `phonegap:build[:platform]` task,
    allows you to override the platforms array for a specific single-platform build (thanks [@bouzuya](https://github.com/bouzuya))
  * Updates `grunt-contrib-nodeunit` to v0.3.0

#### 0.8.0

  * Adds configuration for Android splash screens. (thanks [@arthurgeek](https://github.com/arthurgeek))

#### 0.7.0

The icon configuration API has changed to permit multiple platforms. If you
used this feature before `v0.7.0`, please update your **Gruntfile** as per
the updated example in `README.md`.

  * Adds app icon management for Windows Phone 8 (thanks [@kombucha](https://github.com/kombucha))
  * FIX: Cordova hook file permissions are now preserved (#13)
  * FIX: Release keystore file path can lead to unexpected escaped characters (Windows) (#15)
  * FIX: No longer inadvertantly attempt a device deploy instead of an emulator deploy when the
    emulator option is passed to `phonegap:run` (#17)
  * Reorganizes tests into an improved file structure based on platforms and features
  * Removes indirect references to Grunt's deprecated external (lodash & async) libraries
  * Updates devDependencies `grunt-contrib-coffee`, `grunt-contrib-copy` and `grunt-bump` to their latest versions.

#### 0.6.1
  * FIX: fixAndroidVersionCode not handling the config.versionCode correctly

#### 0.6.0
  * Adds configurable `versionCode` for Android applications

#### 0.5.1
  * Allows you to set the maxBuffer size for child processes

#### 0.5.0
  * Adds app icon management for Android

#### 0.4.2
  * Fixes a regression in 0.4.1 that causes the apk copied into the release directory to contain 0 bytes

#### 0.4.1
  * Adds option to process custom config.xml as a template

#### 0.4.0
  * Adds `release:android` task to build a releases/ directory containing a signed APK for distribution.
  * Includes compiled tasks/ directory in source countrol
  * Removes `phonegap` npm dependency - install it globally with -g instead.

#### 0.3.0

  * Fixes [issue #2](https://github.com/logankoester/grunt-phonegap/issues/2) "Test not completing" (thanks [@skarjalainen](https://github.com/skarjalainen) and [@jrvidal](https://github.com/jrvidal))
  * Removed default 'device' flag (thanks [@robwalch](https://github.com/robwalch))

#### 0.2.0

  * Adds 'config' option for specifying a custom path to 'config.xml'.

#### 0.1.0

  * Initial release

## License

Copyright (c) 2013-2014 Logan Koester.
Released under the MIT license. See `LICENSE-MIT` for details.


[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/logankoester/grunt-phonegap/trend.png)](https://bitdeli.com/free "Bitdeli Badge")

[![xrefs](https://sourcegraph.com/api/repos/github.com/logankoester/grunt-phonegap/badges/xrefs.png)](https://sourcegraph.com/github.com/logankoester/grunt-phonegap)
[![funcs](https://sourcegraph.com/api/repos/github.com/logankoester/grunt-phonegap/badges/funcs.png)](https://sourcegraph.com/github.com/logankoester/grunt-phonegap)
[![top func](https://sourcegraph.com/api/repos/github.com/logankoester/grunt-phonegap/badges/top-func.png)](https://sourcegraph.com/github.com/logankoester/grunt-phonegap)
[![library users](https://sourcegraph.com/api/repos/github.com/logankoester/grunt-phonegap/badges/library-users.png)](https://sourcegraph.com/github.com/logankoester/grunt-phonegap)
[![authors](https://sourcegraph.com/api/repos/github.com/logankoester/grunt-phonegap/badges/authors.png)](https://sourcegraph.com/github.com/logankoester/grunt-phonegap)
[![Total views](https://sourcegraph.com/api/repos/github.com/logankoester/grunt-phonegap/counters/views.png)](https://sourcegraph.com/github.com/logankoester/grunt-phonegap)
[![Views in the last 24 hours](https://sourcegraph.com/api/repos/github.com/logankoester/grunt-phonegap/counters/views-24h.png)](https://sourcegraph.com/github.com/logankoester/grunt-phonegap)
