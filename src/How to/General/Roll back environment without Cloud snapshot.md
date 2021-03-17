---
title: Roll back environment without Cloud snapshot
link: https://support.magento.com/hc/en-us/articles/360031627051-Roll-back-environment-without-Cloud-snapshot
labels: Magento Commerce Cloud,Cloud,2.2,2.1,snapshot,uninstall,roll back,commit,2.2.x,2.1.x,how to
---

This article shows two solutions to roll back an environment without having a snapshot of your environment on Magento Commerce Cloud.

### Affected products and versions

* Magento Commerce Cloud, [all supported versions](https://magento.com/sites/default/files/magento-software-lifecycle-policy.pdf)

Choose the most appropriate for your case:

* If you have a stable build, but no valid snapshot - [Scenario 1: No snapshot, build stable (SSH connection available)](scen2).
* If the build is broken and you have no valid snapshot - [Scenario 2: No snapshot; build broken (no SSH connection)](scen3).

## Scenario 1: No snapshot, build stable (SSH connection available)

This section shows how to roll back an environment when you have not created a snapshot but can access the environment via SSH.

The steps are:

1. Disable Configuration Management.  
1. Uninstall the Magento software.  
1. Reset the git branch.

After performing these steps:

* your Magento installation returns to its Vanilla state (database restored; deployment configuration removed; directories under \`var\` cleared)
* your git branch is reset to the desired state in the past

Read the detailed steps below.

### Step 0 (Prerequisite): Remove config.php to disable Configuration Management

We need to disable Configuration Management so that it does not automatically apply the previous configuration settings during deployment.

To disable Configuration Management, make sure that your `` /app/etc/ `` directory does not contain the `` config.php `` (for Magento 2.2.x) or `` config.local.php `` (for Magento 2.1.x) files.

To remove the configuration file, follow these steps:

1. [SSH to your environment](http://devdocs.magento.com/guides/v2.2/cloud/env/environments-ssh.html#ssh).
1. Remove the configuration file:
    
    * For Magento 2.2:  
        
        
        <pre><code class="language-php">rm app/etc/config.php</code></pre>
        
        
    * For Magento 2.1:  
        
        
        <pre><code class="language-php">rm app/etc/config.local.php</code></pre>
        
        
    
    
    

Read more about Configuration Management:

* [Knowledge Base](https://support.magento.com/hc/en-us/articles/115003169574)
* [DevDocs](http://devdocs.magento.com/guides/v2.2/cloud/live/sens-data-over.html)

### Step 1: Uninstall the Magento software with setup:uninstall command

>  
> Uninstalling the Magento software drops and restores the database, removes the deployment configuration, and clears directories under \`var\`.
> 
> Magento DevDocs, [Uninstall the Magento software](http://devdocs.magento.com/guides/v2.2/install-gde/install/cli/install-cli-uninstall.html#instgde-install-uninstall)
> 

To uninstall the Magento software, follow these steps:

1. [SSH to your environment](http://devdocs.magento.com/guides/v2.2/cloud/env/environments-ssh.html#ssh).
1. Execute `` setup:uninstall ``:  
    
    
    <pre><code class="language-php">php bin/magento setup:uninstall</code></pre>
    
    
1. Confirm uninstall.

The following message displays to confirm a successful uninstallation:

<pre><code class="language-php">[SUCCESS]: Magento uninstallation complete.</code></pre>

This means we have reverted our Magento installation (including DB) to its authentic (Vanilla) state.

### Step 2: Reset the git branch

With git reset, we revert the code to the desired state in the past.

1. Clone the environment to your local development environment. You may copy the command in your Project Web Interface:  
    ![copy_git_clone.png](https://support.magento.com/hc/article_attachments/360000963074/copy_git_clone.png)
1. Access the commits history. Use `` --reverse `` to display history in reverse order for more convenience:  
    
    
    <pre><code class="language-git">git log --reverse</code></pre>
    
    
1. Select the commit hash on which you've been good. To reset code to its authentic state (Vanilla), find the very first commit that created your branch (environment).  
    ![Selecting a commit hash in git console](https://support.magento.com/hc/article_attachments/360000945733/select_commit_hash.png)
1. Apply hard git reset:  
    
    
    <pre><code class="language-git">git reset --h &lt;commit_hash></code></pre>
    
    
1. Push changes to server:  
    
    
    <pre><code class="language-git">git push --force &lt;origin> &lt;branch></code></pre>
    
    

After performing these steps, our git branch gets reset and the entire git changelog is clear. The last git push triggers the redeploy to apply all changes and re-install Magento.

## Scenario 2: No snapshot; build broken (no SSH connection)

This section shows how to roll back an environment when it is in a critical state: the deployment procedure cannot succeed in building a working application, thus making the SSH connection unavailable.

In this scenario, you must first restore the working state of your Magento application using git reset, then uninstall the Magento software (to drop and restore the database, remove the deployment configuration, etc.). The scenario involves the same steps as in Scenario 1, but the order of steps is different and there is an additional step — force redeploy. The steps are:

[1. Reset the git branch.](https://support.magento.com/hc/en-us/articles/360000852534#reset-git-branch)  
[1. Disable Configuration Management.](https://support.magento.com/hc/en-us/articles/360000852534#disable_config_management)  
[1. Uninstall the Magento software.](https://support.magento.com/hc/en-us/articles/360000852534#setup-uninstall)  
1. Force redeploy.

After performing these steps, you will have the same results as in Scenario 1. ### Step 4: Force redeploy

Make a commit (this might be an empty commit, although we do not recommend it) and push it to the server to trigger redeploy:

<pre><code class="language-git">git commit --allow-empty -m "&lt;message>" &amp;&amp; git push &lt;origin> &lt;branch></code></pre>

## If setup:uninstall fails, reset database manually

If executing the `` setup:uninstall `` command fails with an error and cannot be completed, we may clear the DB manually with these steps:

1. [SSH to your environment](http://devdocs.magento.com/guides/v2.2/cloud/env/environments-ssh.html#ssh).
1. Connect to the MySQL DB:  
    
    
    <pre><code class="language-sql">mysql -h database.internal</code></pre>
    
    
1. Drop the \`main\` DB :  
    
    
    <pre><code class="language-sql">drop database main;</code></pre>
    
    
1. Create an empty \`main\` DB:  
    
    
    <pre><code class="language-sql">create database main;</code></pre>
    
    
1. Delete the following configuration files: `` config.php ``, `` config.php ``.bak, `` env.php ``, `` env.php.bak ``.

After resetting the DB, [make a git push to the environment to trigger redeploy](https://devdocs.magento.com/guides/v2.3/cloud/reference/cli-ref-topic.html#git-commands) and install Magento to a newly created DB. Or [run the redeploy command](https://devdocs.magento.com/guides/v2.3/cloud/reference/cli-ref-topic.html#environment-commands).

## Related reading

* [Restore a snapshot on Cloud](https://devdocs.magento.com/guides/v2.2/cloud/project/project-webint-snap.html#restore-snapshot)
* [Create a snapshot](https://devdocs.magento.com/guides/v2.2/cloud/project/project-webint-snap.html#create-snapshot)
* [Snapshots and backup management](https://devdocs.magento.com/guides/v2.3/cloud/project/project-webint-snap.html)
* [Configure your project - View environment history](https://devdocs.magento.com/guides/v2.3/cloud/project/project-webint-basic.html#project-conf-hist)
* [Component deployment failure](https://devdocs.magento.com/guides/v2.3/cloud/trouble/trouble_comp-deploy-fail.html)
* [Manage your project](https://devdocs.magento.com/guides/v2.3/cloud/project/projects.html)