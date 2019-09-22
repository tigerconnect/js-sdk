# TigerConnect JS SDK

<!-- MarkdownTOC depth=3 autolink=true bracket=round -->

  - [Overview](#overview)
  - [Installation](#installation)
    - [Web](#web)
    - [Node](#node)
  - [Contact](#contact)
  - [License](#license)

<!-- /MarkdownTOC -->

## Overview

The TigerConnect JS SDK provides a simple way to enhance your web applications with intelligent, secure messaging from TigerConnect.

This repository contains binary distributions of the JS SDK released by [TigerConnect](https://tigerconnect.com/).

In order to use the TigerConnect JS SDK you must be a registered developer. All aspects of this setup are covered in detail in the [TigerConnect Developer Portal](https://developer.tigertext.com/docs).

Full documentation for the JS SDK is maintained on our [TigerConnect SDK Documentation page](https://tigerconnect.github.io/js-sdk).

The SDK supports both web and node.js.

## Installation

### Web

#### Using npm

```sh
npm install --save tigerconnect
```

```js
const TigerConnect = require('tigerconnect');
const client = new TigerConnect.Client(config);
```

Webpack and most other bundlers support automatically finding the web-specific version of the package at `tigerconnect-sdk-web.min.js`. If your bundler does not support this, or you are not using a bundler, then you may need to use an explicit path like this instead:

```js
const TigerConnect = require('tigerconnect/tigerconnect-sdk-web.min.js');
const client = new TigerConnect.Client(config);
```

#### Using [Bower](http://bower.io)

```sh
bower install --save tigerconnect
```

```html
<script src="bower_components/tigerconnect/tigerconnect-sdk-web.min.js"></script>
```

#### Manual Include

Download [tigerconnect-sdk-web.min.js](https://raw.githubusercontent.com/tigerconnect/js-sdk/master/tigerconnect-sdk-web.min.js) and include it in a `script` tag:

```html
<script src="path/to/tigerconnect-sdk-web.min.js"></script>
```

### Node.js

```sh
npm install --save tigerconnect
```

```js
const TigerConnect = require('tigerconnect');
const client = new TigerConnect.Client(config);
```

## Contact

If you have any questions please reach out to the TigerConnect team any time by emailing [developersupport@tigerconnect.com](mailto:developersupport@tigerconnect.com?Subject=JS%20SDK%20Help.

## License

The TigerConnect JS SDK is licensed under our [Developer Terms of Use](https://tigerconnect.com/terms/end-user-terms-use-license-agreement/).
