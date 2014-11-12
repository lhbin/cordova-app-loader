cordova-app-loader
==========
> Remote update your Cordova App

Based on [cordova-promise-fs](https://github.com/markmarijnissen/cordova-promise-fs) and [cordova-file-cache](https://github.com/markmarijnissen/cordova-file-cache).

## Getting started

```bash
  # fetch code using bower
  bower install cordova-app-loader cordova-promise-fs
  # ...or npm...
  npm install cordova-app-loader cordova-promise-fs
  
  # install Cordova and plugins
  cordova platform add ios@3.7.0
  cordova plugin add org.apache.cordova.file
  cordova plugin add org.apache.cordova.file-transfer
```

**IMPORTANT:** For iOS, use Cordova 3.7.0 or higher (due to a [bug](https://github.com/AppGyver/steroids/issues/534) that affects requestFileSystem).

Or just download and include [CordovaPromiseFS.js](https://raw.githubusercontent.com/markmarijnissen/cordova-promise-fs/master/dist/CordovaPromiseFS.js) and [CordovaAppLoader.js](https://raw.githubusercontent.com/markmarijnissen/cordova-app-loader/master/www/lib/CordovaAppLoader.js)

## Demo

Check out [Cordova App Loader](http://data.madebymark.nl/cordova-app-loader/index.html) in Chrome for a demo! (**Chrome only!**)

```bash
git clone git@github.com:markmarijnissen/cordova-app-loader.git
cd cordova-app-loader
cordova platform add ios@3.7.0
cordova plugin add org.apache.cordova.file
cordova plugin add org.apache.cordova.file-transfer
cordova run ios
```

Want to run your own server? Modify `serverRoot` in `www/test/test.js`!

## Usage

### Step 1: Write a `manifest.json`
```json
{
  "files":{ // Files to download. Only newer versions will be downloaded!
    "lib/jquery.min.js": { "version": 0 },
    "lib/bluebird.js": { "version": 0 },
    "lib/CordovaPromiseFS.js": { "version": 0 },
    "lib/CordovaAppLoader.js": { "version": 0 },
    "test/template.html": {"version": 0},
    "test/test.js": { "version": 0 },
    "update2/index.js": { "version": 2 },
    "update2/style2.css": { "version": 2 }
  }, 

  "load":[ // Files to insert when bootstrapping app.
    "lib/jquery.min.js",
    "lib/bluebird.js",
    "lib/CordovaPromiseFS.js",
    "lib/CordovaAppLoader.js",
    "test/test.js",
    "update2/index.js",
    "update2/style2.css"
  ]
}
```

You can automatically create a version based on the checksum of the file:
```bash
bin/update-manifest www www/manifest.json
```

### Step 2: Add [bootstrap.js](https://raw.githubusercontent.com/markmarijnissen/cordova-app-loader/master/www/bootstrap.js) to your [index.html](https://raw.githubusercontent.com/markmarijnissen/cordova-app-loader/master/www/index.html)

```html
  <script type="text/javascript" timeout="5000" manifest="update1/manifest.json" src="bootstrap.js"></script>
```

1. The `manifest.json` is downloaded once. Second time it is retrieved from localStorage.
2. Javascript and CSS is inserted on the page.
3. Your javascript codes calls `window.BOOTSTRAP_OK = true` to indicate bootstrap was a success.
4. If the manifest is updated and fails to bootstrap the app after `timeout` milliseconds, the manifest in localStorage is deleted and the app reloads the original manifest (and application).

**Notes:**
* The `manifest` attribute should point to a local manifest (i.e. bundled with the app) to guarantee the app can bootstrap even without internet connection.
* Don't forget to set `BOOTSTRAP_OK` to `true`!

### Step 3: Use CordovaAppLoader to `check`, `download` and `update` your app.
```javascript
// Step 3a: Initialize
var fs = new CordovaPromiseFS({});
var loader = window.loader = new CordovaAppLoader({
  fs: fs,
  localRoot: 'app',
  mode: 'mirror'
});

// Step 3b: Check for updates
loader.check().then( ... )  // used `serverRoot` in your `manifest.json`
loader.check('http://yourserver.com/manifest.json').then( ... ) // custom `manifest.json` url
loader.check({ files: { ... } }).then( ... ) // or just check an actual Manifest object.

// Step 3c: Download
loader.download(onprogress).then(function(manifest){},function(failedDownloadUrlArray){ ... });

// Step 4d: Update
loader.update() // reloads page to bootstrap new manifest. Returns true if app will be updated.
```

**Note:** When downloading and updating, the new manifest is written to localStorage. The `manifest.root` is set to the location of the downloaded files. (Default is `""` - i.e. it loads files relative to your `index.html`)


## Changelog

### 0.3.0 (13/11/2014)

* Chrome support!

### 0.2.0 (09/11/2014)

* Improved app layout
* Added test-cases to the app (slow, broken app, broken download)
* Several bugfixes

### 0.1.0 (07/11/2014)

* First release

## Contribute

Convert CommonJS to a browser-version:
```bash
npm install webpack -g
npm run-script prepublish
```

Feel free to contribute to this project in any way. The easiest way to support this project is by giving it a star.

## Contact
-   @markmarijnissen
-   http://www.madebymark.nl
-   info@madebymark.nl

© 2014 - Mark Marijnissen