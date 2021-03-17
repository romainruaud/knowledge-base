---
title: Magento Commerce Cloud repo could not be accessed: 403 Forbidden or 404 Not Found error when deploying
link: https://support.magento.com/hc/en-us/articles/360040296392-Magento-Commerce-Cloud-repo-could-not-be-accessed-403-Forbidden-or-404-Not-Found-error-when-deploying
labels: Magento Commerce Cloud,authentication,update,access key,deployment error,URL could not be accessed: HTTP/1.1 403 Forbidden,2.3.x,2.2.x,how to
---

This article discusses how to resolve Magento Commerce Cloud failed deployment with an error similar to the following:  
  
"_The 'https://repo.magento.com/archives/magento/magento-cloud-configuration/magento-magento-cloud-configuration-x.x.x.x.zip' URL could not be accessed: HTTP/1.1 403 Forbidden_".  
  
Or  
  
The "_https://repo.magento.com/archives/magento/module-customer-segment/magento-module-customer-segment-102.0.5.0-patch2.zip" file could not be downloaded (HTTP/1.1 404 Not Found)_"

## Affected products and versions

* Magento Commerce Cloud 2.2.x, 2.3.x

## Issue

Steps to reproduce

Trigger deployment manually or by performing a merge, push, or synchronization of your environment.

Actual result

Deployment gets stuck. In the deployment error log in the Project UI, an error message similar to the following is displayed: _"The 'https://repo.magento.com/archives/magento/magento-cloud-configuration/magento-magento-cloud-configuration-x.x.x.x.zip' URL could not be accessed: HTTP/1.1 \[403 Forbidden or 404 Not Found\]"_. 

(Click the "Failure" icon in the Project UI to see the log.)

Expected result

Deployment is completed successfully.

## Cause

The error is caused by the authorization keys (access keys) being not valid, not specified or not specified correctly.

Some of the reasons for keys being not valid are:

* You generated the keys using your shared account.
* Your license was previously revoked due to payment issues.

<p class="info">Customers get reminders and also get a 30 day grace period, so normally when credentials stop working, it has been almost 60 days since payment should have been received. You can't open a new support ticket and deploy the application. After you get your license activated again, you still can't deploy the application.</p>

## Solution

Take the following steps to solve the issue with the authorization keys (see the sections below for more details on each step):

1. Obtain the valid authorization keys (skip this if you are absolutely sure your key is valid).
1. Add the keys value in the `` env:COMPOSER_AUTH `` variable (or make sure that the correct value is there) and check if the keys are specified consistently in the variable and the `` auth.json `` file in the project root. 
1. Update or delete <code class="c-mrkdwn__code" data-stringify-type="code">auth.json</code>, to have a single place where the key is configured, if the authorization keys values are not specified or have an other value.

### 1. Obtain valid authorization keys

If you were using the keys created under the shared account, you need to contact the Magento license owner who provides you access and request them to generate the keys for you.

If your license was previously revoked due to payment issues, and you have resolved those issues and your license was renewed, you need to [generate the new authentication keys](https://devdocs.magento.com/guides/v2.3/install-gde/prereq/connect-auth.html). 

### 1. Add the keys value in the env:COMPOSER\_AUTH variable and check if the same keys are specified in auth.json

See the instructions and related information in [Prepare your existing Magento Commerce install > ](https://devdocs.magento.com/cloud/setup/first-time-setup-import-prepare.html#auth-json)

[Add Magento authentication keys](https://devdocs.magento.com/cloud/setup/first-time-setup-import-prepare.html#auth-json) in Magento Developer Documentation.

### 1. Update or delete auth.json

Following is a step by step description of how to update your authorization keys: 

1. Log in to the machine that has your Magento Commerce Cloud SSH keys.
1. Log in to your project:
    
        magento-cloud login
    
    
1. Create a branch to update the code (in the following example the branch name is `` auth `` is created from the primary branch):
    
        magento-cloud environment:branch auth master
    
    
1. Change to the project root directory.
1. Optional: Delete the `` auth.json `` if you prefer and continue to [step 9](#step9).
1. Open `` auth.json `` in a text editor.
    
    <pre><code class="language-json"> {
   "http-basic": {
      "repo.magento.com": {
         "username": "&lt;public_key>",
         "password": "&lt;private_key>"
      }
   }
}</code></pre>
    
    
1. Add the correct authentication keys.
1. Save your changes and exit the text editor.
1. Commit and merge your changes.
1. git add -A
    
    
    
        git commit -m "&lt;message>"
    
    
    
        git push origin master
    
    
1. Wait for the project to deploy.

 