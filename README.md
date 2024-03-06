## Overview

This repository is intended for streamlining the process of reproducing of MetaMask bug.

## The Bug

The problem occurs when web page starts to interact with MetaMask shortly after a fresh browser instance is started (important: not a new tab/page is opened in already running browser instance). Apparently, the MetaMask extension takes time to initialize, and until it's done the web app should not call any MetaMask API methods.

What is really bad is that the call to `request` method returns a Promise which hangs forever, not giving any result or error, which negatively affects the web application behavior in what seems like a random manner which is pretty hard to debug.

Moreover, it's unclear how to properly wait until the MetaMask extension initialiation process is finished, in order to adjust the web app initialization logic accordingly.

Whilst the problem won't affect 99% of web app users who open it in a new tab of already running browser instance, it does affect web app developers who are frequently starting new browser instances for debugging and/or automated testing purposes.

Similar issues has already been submitted [here](https://github.com/MetaMask/metamask-extension/issues/14283) and [here](https://github.com/MetaMask/metamask-extension/issues/19290) but the issue is still not fixed, as per the latest MetaMask Chrome extension.

## Test case

In this simple example, the web page uses MetaMask API to get a list of previosly connected accounts.

### Initial state

The page has "Loading..." text

### Expected outcome

The page text changes to "Accounts: 0".
Generally acceptable option would be to get a text changed to "Error: ...".

### Actual outcome

The page text stays the same, indicating that the promise will never actually settle, and sometimes there are some errors written to the browser console:

```text
localhost/:1 Unchecked runtime.lastError: Could not establish connection. Receiving end does not exist.
(2) content-script-start.js:3543 Uncaught (in promise) Error: Could not establish connection. Receiving end does not exist.
    at wrappedSendMessageCallback (content-script-start.js:3543:20)
```

If the browser page is then refreshed, the expected outcome is reached.

## Prerequisites

- This repository was made and tested on Ubuntu 23.10 with Node 21.5.
- You must have Google Chrome browser in your system with MetaMask (or MetaMask Flask) extension pre-installed.
- You must close all Google Chrome browser instances before running this example.

## Usage

1. Install dependencies:
   ```bash
   npm i
   ```
1. Run the web server:
   ```bash
   npm start
   ```
1. Open a test page in Chrome browser:
   ```bash
   npm run browse
   ```
