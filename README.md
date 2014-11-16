Phonegap Air
============

No-compromise web development. Put your web app in the iOS App Store and update it whenever you want. Access native features like the contacts list and camera using PhoneGap APIs. Bridge the uncanny valley of hybrid apps with launch images and pre-caching.

## Contents

1. [Features](#features)
2. [Demo](#demo)
3. [The App Manifest](#the-app-manifest)
4. [Configuring the Xcode project](#configuring-the-xcode-project)
   1. [Endpoint URLs](#endpoint-urls)
   2. [Launch Images & App Icons](#launch-images--app-icons)
   3. [Restrict Dev Tools Access](#restrict-dev-tools-access)
   4. [Other Preferences](#other-preferences)
5. [FAQ](#faq)
   * [Is This Allowed?](#is-this-allowed)
   * [When Does The App Update?](#when-does-the-app-update)
6. [Support](#support)
7. [License](#license)

## Features

* Update any asset you want. HTML, CSS, JS, Fonts, Images, etc.
* Keep your app up-to-date even if the user hasn't launched it in a while
* Download only the smallest changeset needed to update a client
* Switch between prod/staging/dev versions of your app by shaking your device
* Make your app feel faster with cleverly used launch images

**Advanced Features**

* Load images from the web instantly by proxying them to a local cache
* Deep link into your application (`myapp://do-something`)

## Demo

![Demo App](https://cldup.com/M5ZzPZAiEA-3000x3000.jpeg)

Included is a working Xcode project and sample node.js server.

1. Open the Xcode project
2. Run it in the iOS simulator (pick any device)
3. Observe that a blocking update is performed using the production manifest
4. Select Hardware -> Shake Gesture to open the branch switcher
5. Select "Staging"
6. Observe that a blocking update was performed using the staging manifest

### The App Manifest

Your server must host a `manifest.json` that lists the files and assets your application needs to function.

**example**
```json
{
  "files":{
    "app.js": {
      "checksum":"d8138338a247ec7ec1eb69c40dc554c2",
      "destination":"app.js",
      "source":"/app.js"
    }
  },
  "assets":["http://cdn.com/image.jpg"],
  "message":"A description of this version",
  "version":"1.0.0"
}
```

**files**

A dictionary of objects, each with the following structure:

```json
{
  "checksum":"d8138338a247ec7ec1eb69c40dc554c2",
  "destination":"app.js",
  "source":"/app.js"
}
```

The keys of dictionary are for your own reference; the update system does not use them. `destination` refers to where in the `www` folder this file should be placed. `source` should be a path relative to your web server's root indicating where the file should be downloaded from. The checksum is an md5 hash of the file data (computed with `crypto.createHash('md5').update(buffer).digest('hex')`).

Every file that your app needs to function should be declared here. Nonessential external files such as images on an external CDN should be declared as [assets][manifest-assets].

**assets**

An array of URLs to prime the app's cache with. The content will be associated with the URL, so if if you wish to change the content, you must provide a unique URL or existing clients will continue using the old data.

**message**

A description of this particular version of the app. I recommend using the last commit message.

**version**

The version number of the app, such as `1.0.0`. Clients will update themselves if this increases.

See `ota-server/server.js` for an example.

## Configuring The Xcode Project

### Endpoint URLs

Edit `classes/Constants.m` to suit your application. Three endpoint URLs can be configured. `ProductionURL` is the default endpoint, while `StagingURL` is provided as a shortcut in the dev tools. `CustomURL` is a placeholder for the editable text field in the dev tools.

`ManifestPath` should be relative to the three endpoint URLs. For example, if `ProductionURL` was `http://google.com` and `ManifestPath` was `m.json`, the app would expect to find the manifest at `http://google.com/m.json`.

### Launch Images & App Icons

Consult the [iOS Documentation](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW2) for the full list of icon and launch image sizes.

### Restrict Dev Tools Access
Implement `function shouldAllowOTADevTools()` as a global function in your application. This synchronous function should return a boolean indicating whether or not the dev tools should be opened.

### Other Preferences

There are other preferences you can set, too numerous to list in this guide. The most important one is the Product Name, which is the name of the app in the

## FAQ

### Is This Allowed?

Yes, Apple has changed its long-standing policy against this practice and now allows over-the-air updates of hybrid apps *so long as the purpose of the app does not change*. I have successfully gotten two apps using this technology accepted on the first try, explicity stating in my app review notes that over-the-air updates are performed.

### When Does The App Update?

There are four ways that an update can happen

1. On initial launch, an update is attempted before the app is started
2. When the app enters the background (e.g. when the home button is pushed)
3. When the device is plugged in and on wifi
4. When a selection is made in the dev panel

If an update finishes downloading while the app is in the foreground, it will only be applied when the user reopens the app.

## Support

* Bugs

Let me know how to reproduce it and I'll fix it.

* Documentation Requests

Let me know what's confusing and I'll add to it.

* Enhancements
* Debugging Help
* Consultation

I'm a full-time student with a near full-time job, so these will be a very low priority for me. If you *really* need something @mention me on [twitter](https://twitter.com/_benng) or email me at `me [at] benng.me` and we'll work something out.

## License

The MIT License (MIT)

Copyright (c) 2014 Ben Ng

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
