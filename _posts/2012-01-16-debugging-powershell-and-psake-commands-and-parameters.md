---
id: 256
title: Debugging Powershell and Psake commands and parameters
date: 2012-01-16T18:31:53+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=256
permalink: /2012/01/16/debugging-powershell-and-psake-commands-and-parameters/
sfw_comment_form_password:
  - 1rvgPaqgDQEU
categories:
  - DevOps
tags:
  - powershell
  - psake
---
Getting commands and parameters in Powershell and Psake can be pretty troublesome at times. The echoargs helper from the [PowerShell Community Extensions](http://pscx.codeplex.com/) can be a lifesaver. If, for instance, you are calling

<pre>msbuild.exe /t:Build /p:SomeTroublesomeParametersHere</pre>

if you swap msbuild for echoargs (after placing the extensions in C:\Windows\System32\WindowsPowerShell\v1.0\Modules and calling Import-Module pscx), then you&#8217;ll see the exact parameters being passed to the executable:

<pre>Arg 0 is &lt;/t:Build&gt;
Arg 1 is &lt;/p:SomeTroublesomeParametersHere&gt;</pre>

However, to make it easier for those dipping into our build scripts, I decided to create a wrapper function so that we would always output parameters to the external functions we call to the console for debugging purposes.

<pre>function Invoke-EchoCommand
{
    $cmdName = $args[0];
    $remainingArgs = $args[1..$args.Length]
    Write-Host "Executing $cmdName" -ForegroundColor darkgray
    & "$base_dir/tools/build/echoargs" @remainingArgs | Write-Host -ForegroundColor darkgray
    exec { & $cmdName @remainingArgs } # using exec helper in PSake
}</pre>

You can then replace any calls to exec with

<pre>Invoke-EchoCommand msbuild.exe /t:Build /p:SomeTroublesomeParametersHere</pre>

and you&#8217;ll get some helpful debug information over how the parameters were actually passed. The only nifty bit in the function is that it uses the first parameter as the command name, and then passes all remaining arguments on to the executed command.