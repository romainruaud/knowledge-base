---
title: Blank page or redirect loop error when accessing storefront or Magento Admin
labels: Magento Commerce Cloud,Magento Commerce,troubleshooting,redirect,blank,Magento Admin
---

This article provides a solution for the issue when you access Magento store front or backend and you get a blank page or redirect loop.

### Affected products and versions

* Magento Commerce Cloud, all versions
* Magento Commerce, all versions
* Magento Open Source, all versions

## Issue

Steps to reproduce

Open a store front or Admin page.

Expected result

The page opens.

Actual result

The page is blank or displays the _"This webpage has a redirect loop"_ error message. 

## Cause

One of most probable reasons for the issue is that Magento is set to redirect from unsecure URL to secure URL, but an unsecure URL is given as the value for the secure URL setting.

To fix the issue, you need to correct the value of the secure link.

## Solution

To make sure this is the cause of the problem, take the following steps:

1. Check the `` web/secure/enable_upgrade_insecure ``, `` web/secure/use_in_adminhtml `` (if you have the blank/loop redirect issue in Admin) or `` web/secure/use_in_frontend `` (if you have the blank/loop redirect issue on the store front) value in the `` 'core_config_data' `` DB table.
    
    * If `` web/secure/enable_upgrade_insecure `` is set to "1', then Magento is setup to add the response header <code class="language-html">Content-Security-Policy: upgrade-insecure-requests</code>, thus instructing browsers to use HTTPS, redirecting all queries that come over HTTP to HTTPS, for both Admin and store front.
    * If `` web/secure/use_in_adminhtml `` is set to "1", Magento returns HTTPS redirects for all HTTP requests for the Admin pages.
    * If `` web/secure/use_in_frontend `` is set to "1", Magento returns HTTPS redirects for all HTTP requests for the store front pages.
    
    
    
1. Check the `` web/secure/base_url `` and `` web/unsecure/base_url `` values in the `` 'core_config_data' `` table. If they both start with <code class="language-html">http</code>, then you need to correct the "secure" value.

Fixing the issue:

1. Set the value starting with <code class="language-html">https</code> for `` web/secure/base_url.  ``
1. For the changes to be applied, clean the configuration cache by running the following command:
    
    <pre><code class="language-bash">php &lt;your_magento_install_dir>/bin/magento cache:clean config</code></pre>
    
    

 