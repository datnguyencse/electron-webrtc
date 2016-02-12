# electron-webrtc

[![npm version](https://img.shields.io/npm/v/electron-webrtc.svg)](https://www.npmjs.com/package/electron-webrtc)
[![Build Status](https://travis-ci.org/mappum/electron-webrtc.svg?branch=master)](https://travis-ci.org/mappum/electron-webrtc)
[![Dependency Status](https://david-dm.org/mappum/electron-webrtc.svg)](https://david-dm.org/mappum/electron-webrtc)

Use WebRTC in Node.js via a hidden Electron process

WebRTC is a powerful web API that lets browsers make peer-to-peer connections, and has already been
deployed in [many popular browsers](http://caniuse.com/#feat=rtcpeerconnection). It may sometimes be
useful to let Node.js programs use WebRTC, e.g. in [`webtorrent-hybrid`](https://github.com/feross/webtorrent-hybrid). However, the modules for WebRTC in Node ([`node-webrtc`](https://github.com/js-platform/node-webrtc) and [`node-rtc-peer-connection`](https://github.com/nickdesaulniers/node-rtc-peer-connection)) are either hard to install, broken, or incomplete.

As a hack, this module talks to an invisible Electron instance in the background (using [`electron-eval`](https://github.com/mappum/electron-eval)) to use Chromium's built-in WebRTC implementation.

## Status

This module is compatible with [`simple-peer`](https://github.com/feross/simple-peer) and passes its tests.

`electron-webrtc` is intended for use with RTCDataChannels, so the MediaStream API is not supported.

## Usage

`npm install electron-webrtc`

```js
// call exported function to create Electron process
var wrtc = require('electron-webrtc')()

// uses the same API as the `wrtc` package
var pc = new wrtc.RTCPeerConnection(config)

// compatible with `simple-peer`
var peer = new SimplePeer({
  initiator: true,
  wrtc: wrtc
})
```

### Methods

#### `var wrtc = require('electron-webrtc')()`

Calling the function exported by this module will create a new hidden Electron process. It is recommended to only create one, since Electron uses a lot of resources.

The object returned by this function has the same API as the [`node-webrtc`](https://github.com/js-platform/node-webrtc) package.

#### `wrtc.electronDaemon.close()`

Closes the Electron process and releases its resources. You may not need to do this since the Electron process will close automatically after the Node process terminates.

### Properties

#### `wrtc.electronDaemon`

A handle to the [`electron-eval`](https://github.com/mappum/electron-eval) daemon that this module uses to talk to the Electron process.

### Running on a headless server

Chromium normally won't run on a headless server since it expects a screen that it can render to. So to work around this, we can use `Xvfb`, a utility that creates a framebuffer that Chromium can use as a virtual screen.

First, install `xvfb`:
```sh
sudo apt-get install xvfb
```

Now start it up:
```sh
export DISPLAY='0:99'
Xvfb :99 -screen 0 1024x768x24 > /dev/null 2>&1 &
```

Now you may run your WebRTC code with `electron-webrtc` :)

## Related Modules

- [`node-webrtc`](https://github.com/js-platform/node-webrtc)
- [`node-rtc-peer-connection`](https://github.com/nickdesaulniers/node-rtc-peer-connection)
- [`electron-eval`](https://github.com/mappum/electron-eval)