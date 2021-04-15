---
title: Checkout is stuck when Authorize.net payment method is used
labels: Magento Commerce,Authorize.net,troubleshooting,checkout,2.3.x
---

This article provides an explanation and fix for the Magento Commerce 2.3.X issue where the checkout gets stuck if Authorize.net is used, with the _'Cannot read property 'length' of null'_ error message in the browser console log.

### Affected products and versions

* Magento Commerce 2.3.X

<p class="info">The core Magento Authorize.Net payment integration is deprecated since 2.3.4 and will be completely removed in 2.4.1. Use the <a href="https://marketplace.magento.com/authorizenet-magento-module-authorizenet.html">official extension</a> from marketplace instead.</p>

## Issue

Steps to reproduce

1. Configure the Authorize.net payment method in the Magento Admin.
1. Go to the storefront.
1. Add a product to the cart and proceed to checkout.
1. Choose Authorize.net as a payment method.
1. Click Place Order.

Expected result

The Authorize.net iframe is loaded.

 Actual result 

Ajax spinner is displayed, and the page never loads.  The following JS error is displayed in the browser console log:_ 'Uncaught TypeError: Cannot read property 'length' of null at b (jstest.authorize.net/v1/AcceptCore.js:1)'_

## Cause

One of the most common reasons of this issue is the Public Client Key not being specified in the Authorize.Net configuration in the Magento Admin.

## Solution

Under Stores > Settings > Configuration > Sales > Payment Methods, in the Authorize.net section, check if the value is specified in the Public Client Key field. If it is empty, enter the key value from your Authorize.Net merchant account.

For the changes to be applied, clean the cache by running <code class="language-bash">bin/magento cache:clean</code>.

## Related reading

* [Authorize.net](https://docs.magento.com/m2/ee/user_guide/payment/authorize-net.html) in Magento User Guide.