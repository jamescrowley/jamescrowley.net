---
id: 164
title: Deploying windows services using MsDeploy
date: 2011-09-05T16:36:14+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=164
permalink: /2011/09/05/deploying-windows-services-using-msdeploy/
sfw_comment_form_password:
  - cRaCupkQN6Sq
categories:
  - Uncategorized
---
Running MsDeploy is awesome for automated deployments of websites, but it&#8217;s also possible to use it to deploy other applications to the file system &#8211; such as associated windows services. You just need to jump through a few more hoops to get things up and running.

I&#8217;m using TeamCity for our integration server, but the basic steps will work regardless of the system you are using. I tend to set up TeamCity to have a general &#8220;Build entire solution&#8221; configuration. This builds the entire project in release mode, and performs any config transformations you need (check out my post here if you to [transform app.config files](/2011/01/20/applying-app-config-transformations-in-the-same-way-as-web-config/) for your service).

Next, for each component and configuration we want to deploy (ie website to staging, website to production, services to staging, services to production), I create a new build configuration, with a dependency on the &#8220;build entire solution&#8221; configuration. This means we can assume that the build has completed successfully.

After the build, there&#8217;s a few steps that need to complete:

  * Stop the existing service and uninstall it
  * Copy over the output from the build to the target deployment server
  * Install the new service and start it

**Stopping and starting the services**

For the first and last steps, we can define two simple batch files for each, with a hard coded path of where we&#8217;ll install the service on the target server.

_MyServiceName.PreSync.cmd_

<span style="font-family: monospace;">net stop MyServiceName<br /> C:\Windows\Microsoft.NET\Framework\v4.0.30319\InstallUtil.exe /u /name=MyServiceName &#8220;C:\Program Files\PathTo\MyServiceName.exe&#8221;<br /> sleep 20<br /> </span>

_MyServiceName.PostSync.cmd_

`C:\Windows\Microsoft.NET\Framework\v4.0.30319\InstallUtil.exe /name=MyServiceName "C:\Program Files\PathTo\MyServiceName.exe"<br />
net start MyServiceName`

These should be saved in source control as part of your project resources (I put them in a Deploy folder), and so accessible from the build server. These are very basic at the moment &#8211; they could equally be PowerShell scripts doing far more complicated things or accepting configurable parameters &#8211; but this will do us for our example scenario!

We will use MsDeploy&#8217;s preSync and postSync commands to execute these batch files before and after it performs the synchronization on the file system.

**MsDeploy command**

Let&#8217;s now take a look at the MsDeploy command needed:

`"tools/deploy/msdeploy.exe" -verb:sync -preSync:runCommand="<em>%system.teamcity.build.checkoutDir%\tools\deploy\MyServiceName.PreSync.cmd</em>",waitInterval=30000 -source:dirPath="<em>%system.teamcity.build.checkoutDir%\src\MyServiceName\bin\%env.Configuration%</em>" -dest:computerName=https://<em>stagingserver</em>:8172/msdeploy.axd?site=<em>DummyWebSiteName</em>,userName=<em>%env.UserName%</em>,password=<em>%env.Password%</em>,authType=basic,dirPath="<em>C:\Program Files\MyWindowsService\</em>" -allowUntrusted -postSync:runCommand="<em>%system.teamcity.build.checkoutDir%\tools\Deploy\CodeConversion-PostSyncCommand.cmd</em>",waitInterval=30000`

Let&#8217;s just break this down:

  * verb:sync &#8211; we are syncing!
  * preSync:runCommand &#8211; before we perform the deployment, we can pass the path to a batch file that will be streamed to the deployment server and executed. By default, this will be run under a restricted local service account (&#8220;The WMSvc uses a Local Service SID account that has fewer privileges than the Local Service account itself.&#8221; &#8211; [from MSDN](http://technet.microsoft.com/en-us/library/ee619740(WS.10).aspx)).
  * source:dirPath &#8211; this sets the path we want to copy files from. We&#8217;re using a parametrized build template in TeamCity to pass in the full path to the source directory, and the current configuration)
  * dest:computerName &#8211; this is actually several parameters combined. I tried various permutations, and this is what worked best for me. I&#8217;m not using NTLM authentication here (so authType=basic) because my staging and production servers are on an external network. The username and password are for an IIS Management Service user that we&#8217;ll set up in a minute (and are also parametrized by TeamCity &#8211; but you could hard code them here).
  * allowUntrusted &#8211; allows MsDeploy to accept the unsigned certificate from our target server. You don&#8217;t need this if you&#8217;re using an SSL certificate from a trusted authority.
  * postSync:runCommand &#8211; the command we run after a successful deployment.

There&#8217;s one gotcha with the preSync and postSync operations at the moment &#8211; any error codes returned by preSync or postSync (such as being unable to install the service or start it), the whole MsDeploy action still return success. I haven&#8217;t found a nice way round this yet &#8211; you&#8217;d have to write some powershell script to parse the output and detect errors. Microsoft know about the issue so hopefully it will be fixed in the next release.

**Configuring MsDeploy**

Before we try and run this command, we need to set up a few things on the target server we are deploying to. I&#8217;m assuming you&#8217;re already using MsDeploy to deploy websites, and so you can already see IIS Management Service, IIS Manager Permissions, IIS Manager Users, and Management Service Delegation appearing as options under &#8220;Management&#8221; in your main IIS server configuration screen.

  * Create a new IIS user from the IIS Manager Users screen. Alternatively, you can create a Windows user and use that instead.
  * Even though we&#8217;re installing a service, we still need a target IIS website to associate our credentials with. This could be a dedicated empty website (it doesn&#8217;t need to be running) or an existing one. Make sure you replace &#8220;DummyWebSiteName&#8221; in the command above with the name of the actual website you choose. The underlying path doesn&#8217;t matter, as we override the target path as part of our MsDeploy command.
  * Go into &#8220;IIS Manager Permissions&#8221; for the dummy website you are using, click &#8220;Allow user&#8221; and select either the IIS or Windows user you created above.
  * Next, go into &#8220;Management Service Delegation&#8221;. We need to create two permissions &#8211; one so we can deploy the files to the file system, and another so we can run the pre/post sync commands. For the first, click &#8220;Add Rule&#8221;, select &#8220;Blank Rule&#8221; and then type &#8220;contentPath&#8221; in the providers field, * in the actions, set the Path to the one where you are going to deploy the service to. Save that, and add another blank rule.
  * For this second rule, type &#8220;runCommand&#8221; in the providers field, &#8220;*&#8221; in actions, and choose &#8220;SpecificUser&#8221; under the Run As&#8230; Identity Type field. We need to run under elevated permissions in order to stop/start services and install them. Choose a user account that has these credentials.

**File and user account permissions**

In order for everything to work, we need to ensure that MsDeploy can access the folder we&#8217;re deploying to. We also need to extend the Local Service account so that it can impersonate a more elevated user in order to run the console commands necessary to stop/start and install services (note there are security implications for this &#8211; see [MSDN](http://technet.microsoft.com/en-us/library/ee619740(WS.10).aspx) for more details.).

  * Add read/write access to Local Service account to the target deployment folder
  * Run the following command on the console

`sc privs wmsvc SeChangeNotifyPrivilege/SeImpersonatePrivilege/SeAssignPrimaryTokenPrivilege/SeIncreaseQuotaPrivilege`

  * Finally, you need to restart the Web Management Service for this to take effect.

If all has been set up correctly, you should now be all good to go &#8211; services will automatically deploy and get started!

**Ignoring/preserving files**

In a similar fashion to when deploying websites, you may find you wish to preserve logging folders and similar during deployment. You can do this by adding some additional parameters to the MsDeploy command. For instance:

`-skip:objectName=filePath,skipAction=Delete,absolutePath=\\Logs\\.*$`

will preserve any files in the Logs directory.

**Common error messages & troubleshooting**

When starting out with MsDeploy it&#8217;s likely you&#8217;ll hit a fair number of permission denied errors &#8211; without too much more information. Logging is your friend.

**Request logging** &#8211; enabled through the Management Service configuration window in IIS, you will find requests logged to %SystemDrive%\Inetpub\logs\WMSvc

**Failed request tracing** &#8211; enabled through the Management Service Delegation configuration window, click &#8220;Edit Feature Settings&#8221; and &#8220;Enable failed request tracing logs&#8221;. You will find these at C:\inetpub\logs\wmsvc\TracingLogFiles\W3SVC1

**Web Management Service Tracing** &#8211; enabled through a registry key, [described on MSDN](http://technet.microsoft.com/en-us/library/ff729437(WS.10).aspx).

Below I&#8217;ve included some common error messages and some possible causes.

_&#8220;Connected to the destination computer (&#8220;xyz&#8221;) using the Web Management Service, but could not authorize. Make sure that you are using the correct user name and password, that the site you are connecting to exists, and that the credentials represent a user who has permissions to access the site.&#8221;_

Probably because the username and password you are using are invalid (they haven&#8217;t been set up) or do not have permissions set for the particular &#8220;dummy&#8221; website you are targeting.

_&#8220;Could not complete an operation with the specified provider (&#8220;runCommand&#8221;) when connecting using the Web Management Service. This can occur if the server administrator has not authorized the user for this operation.&#8221;_

Most likely you have not set up the correct delegated services through the Management Service Delegation window &#8211; either no runCommand permissions have been set, or the delegated user doesn&#8217;t have permissions to run the command.

_Could not complete an operation with the specified provider (&#8220;dirPath&#8221;) when connecting using the Web Management Service. This can occur if the server administrator has not authorized the user for this operation._ 

Either you haven&#8217;t set the dirPath permissions via the Management Service Delegation window, or the Local Service account does not have read/write access to the specified directory.

<div>
  <em>Error during &#8216;-preSync&#8217;. An error occurred when the request was processed on the remote computer. The server experienced an issue processing the request. Contact the server administrator for more information.</em>
</div>

This occurred for me if you haven&#8217;t given the Web Management Service permissions to impersonate another user using the `sc privs` described above, or you have, but haven&#8217;t restarted the service yet.

_Info: Updating runCommand. Warning: Access is denied. Warning: The process &#8216;C:\Windows\system32\cmd.exe&#8217; (command line &#8216;/c &#8220;C:\Windows\ServiceProfiles\LocalService\AppData\Local\Temp\giz2t0kb.0ay.cmd&#8221;&#8216;) exited with code &#8216;0x1&#8217;._

This occurred for me if I had set the Management Service Delegation for runCommand, but left the service running as it&#8217;s built-in identity rather than &#8220;RunAs&#8221;&#8230; &#8220;Specific user&#8221;.

I hope this helps someone!