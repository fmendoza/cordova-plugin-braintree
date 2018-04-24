# Braintree Cordova Plugin

This is a [Cordova](http://cordova.apache.org/) plugin for the [Braintree](https://www.braintreepayments.com/) mobile payment processing SDK.

This version of the plugin uses versions `4.9.0` (iOS) and `2.6.2` (Android) of the Braintree mobile SDK. Documentation for the Braintree SDK can be found [here](https://developers.braintreepayments.com/start/overview). Before start using this plugin please read that documentation.

**This plugin is still in development.**

# Install

Be sure, that plist and xcode npm module is installed:
```bash
    npm install plist
    npm install xcode
```

To add the plugin to your Cordova project, first remove the iOS platform, install the latest version of the plugin directly from git, and then re-add iOS platform

```bash
    cordova platform remove ios
    cordova plugin add https://github.com/taracque/cordova-plugin-braintree
    cordova platform add ios
```

# Usage

The plugin is available via a global variable named `BraintreePlugin`. It exposes the following properties and functions.

All functions accept optional success and failure callbacks as their last two arguments, where the failure callback will receive an error string as an argument unless otherwise noted.

A TypeScript definition file for the JavaScript interface is available in the `typings` directory as well as on [DefinitelyTyped](https://github.com/borisyankov/DefinitelyTyped) via the `tsd` tool.

## Initialize Braintree Client ##

Used to initialize the Braintree client. The client must be initialized before other methods can be used.

Method Signature:

`initialize(token, successCallback, failureCallback)`

Parameters:

* `token` (string): The unique client token or static tokenization key to use.

Example Usage:

```
var token = "YOUR_TOKEN";

BraintreePlugin.initialize(token,
    function () {
        console.log("init OK!");
        ...
    },
    function (error) { console.error(error); });
```
*As the initialize code is async, be sure you called all Braintree related codes after successCallback is called!* 

## Show Drop-In Payment UI ##

Used to show Braintree's drop-in UI for accepting payments.

Method Signature:

`presentDropInPaymentUI(options, successCallback, failureCallback)`

Parameters:

* `options` (object): An optional argument used to configure the payment UI; see type definition for parameters.

Example Usage:

```
var options = {
    amount: "49.99",
    primaryDescription: "Your Item"
};

BraintreePlugin.presentDropInPaymentUI(options, function (result) {

    if (result.userCancelled) {
        console.debug("User cancelled payment dialog.");
    }
    else {
        console.info("User completed payment dialog.");
        console.info("Payment Nonce: " + result.nonce);
        console.debug("Payment Result.", result);
    }
});
```

## Apple Pay (iOS only) ##

Do not turn on Apple Pay in Braintree if you don't have Apple Pay entitlements.
To allow ApplePay payment you need to initialize Apple Pay framework before usign the Drop/In Payment UI. Read Braintree docs to setup Merchant account: https://developers.braintreepayments.com/guides/apple-pay/configuration/ios/v4#apple-pay-certificate-request-and-provisioning

Method Signature:
`setupApplePay(options)`

Paramteres:

* `options` (object): Merchant settings object, with the following keys:
    *   `merchantId` (string): The merchant id generated on Apple Developer portal.
    *   `currency` (string): The currency for payment, 3 letter code (ISO 4217)
    *   `country` (string): The country code of merchant's residence. (ISO 3166-2)

Example Usage:

```
BraintreePlugin.setupApplePay({ merchantId : 'com.braintree.merchant.sandbox.demo-app', country : 'US', currency : 'USD'});
```

ApplePay shown in Drop-In UI only if `BraintreePlugin.setupApplePay` called before `BraintreePlugin.presentDropInPaymentUI`
