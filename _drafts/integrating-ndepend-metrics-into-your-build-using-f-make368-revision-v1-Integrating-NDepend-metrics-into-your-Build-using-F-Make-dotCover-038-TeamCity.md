---
id: 378
title: 'Integrating NDepend metrics into your Build using F# Make, dotCover &#038; TeamCity'
date: 2014-04-22T20:38:10+00:00
author: james.crowley
layout: revision
guid: http://www.jamescrowley.co.uk/2014/04/22/368-revision-v1/
permalink: /2014/04/22/368-revision-v1/
---
[NDepend](http://www.ndepend.com/) is an analysis tool giving you all kinds of code quality metrics, but also tools to drill down into dependencies, and query and enforce rules over your code base.

There&#8217;s a version that integrates with Visual Studio, but there&#8217;s also a version that runs on the console to generate static reports, and enforce any code rules you might have written.

I&#8217;ve previously depended a little too much on TeamCity to construct our build process, but have been increasingly shifting all our steps (including analysis, code coverage and deployment) to our build scripts (and therefore source control).

So, I wanted to see how easy it would be to combine all of this and use NDepend to generate actionable reports and metrics on our code base &#8211; not just now, but how it shifts over time.

To do this, you need to

  * Run your unit tests via a code coverage tool, such as dotCover. This has a command line version bundled with TeamCity which you are free to use directly.
  * Run NDepend with your code coverage files and NDepend project file
  * Store NDepend metrics from build-to-build so it can track trends over time

We&#8217;re using [F# make](http://fsharp.github.io/FAKE/) in our build process to which I recently contributed some dotCover extensions, which funnily enough makes this process a load easier!

**Downloading your dependencies**

First up, we use the following F# helper and F# Make to download our dotCover and NDepend executables from a HTTP endpoint:

<pre>let ensureToolIsDownloaded toolName downloadUrl =
    if not (TestDir (toolsDir @@ toolName)) then
        let downloadFileName = Path.GetFileName(downloadUrl)
        trace ("Downloading " + downloadFileName + " from " + downloadUrl)
        let webClient = new System.Net.WebClient()
        webClient.DownloadFile(downloadUrl, toolsDir @@ downloadFileName)
        Unzip (toolsDir @@ toolName) (toolsDir @@ downloadFileName)

Target "EnsureDependencies" (fun _ -&gt;
    ensureToolIsDownloaded "dotCover" "https://YourLocalDotCoverDownloadUrl/dotCoverConsoleRunner.2.6.608.466.zip"
    ensureToolIsDownloaded "nDepend" "https://YourLocalDotCoverDownloadUrl/NDepend_5.2.1.8320.zip"
    CopyFile (toolsDir @@ "ndepend" @@ "NDependProLicense.xml") (toolsDir @@ "NDependProLicense.xml")
)
</pre>

We can also use the same mechanism to ensure we have whatever unit testing framework tools in place:

        RestorePackageId (fun p -> { p with OutputPath = "tools"; ExcludeVersion = true; Version = Some (new Version("2.6.3")) }) "NUnit.Runners"

&nbsp;

**NDepend trends**

We treat our build agents as transient, so would rather rely on a mechanism other than just storing on the build machine. TeamCity&#8217;s artifacts mechanism allows us to do this. We can store artifacts each time our build runs, and restore the artifacts from the previous successful build the next time we run. Before this was a bit of a pain but as of TeamCity 8.1 you can now [reference your own artifacts](http://youtrack.jetbrains.com/issue/TW-12984) to allow for incremental-style builds