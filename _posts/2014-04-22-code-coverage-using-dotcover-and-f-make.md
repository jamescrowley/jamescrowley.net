---
id: 379
title: 'Code coverage using dotCover and F# make'
date: 2014-04-22T20:59:37+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=379
permalink: /2014/04/22/code-coverage-using-dotcover-and-f-make/
categories:
  - Software Engineering
---
I&#8217;ve previously depended a little too much on TeamCity to construct our build process, but have been increasingly shifting everything to our build scripts (and therefore source control).

We&#8217;ve been using [F# make](http://fsharp.github.io/FAKE/) &#8211; an awesome cross platform build automation tool like make & rake.

_As an aside (before you ask): The dotCover support in TeamCity is already excellent &#8211; as you&#8217;d expect &#8211; but if you want to use these coverage files elsewhere (NDepend, say), then you can&#8217;t use the out-of-the-box options very easily._

**Downloading your dependencies**

We&#8217;re using NUnit and MSpec to run our tests, and so in order to run said tests, we need to ensure we have the test runners available. Rather than committing them to source control, we can use F# make&#8217;s support for restoring NuGet packages.

<pre class="brush: fsharp; title: ; notranslate" title="">RestorePackageId (fun p -&gt; { p with OutputPath = "tools"; ExcludeVersion = true; Version = Some (new Version("2.6.3")) }) "NUnit.Runners"
</pre>

DotCover is a little trickier, as there&#8217;s no NuGet package available (the command line exe is bundled with TeamCity). So, we use the following helper and create an F# make target called &#8220;EnsureDependencies&#8221; to download our dotCover and NDepend executables from a HTTP endpoint:

<pre class="brush: fsharp; title: ; notranslate" title="">let ensureToolIsDownloaded toolName downloadUrl =
    if not (TestDir (toolsDir @@ toolName)) then
        let downloadFileName = Path.GetFileName(downloadUrl)
        trace ("Downloading " + downloadFileName + " from " + downloadUrl)
        let webClient = new System.Net.WebClient()
        webClient.DownloadFile(downloadUrl, toolsDir @@ downloadFileName)
        Unzip (toolsDir @@ toolName) (toolsDir @@ downloadFileName)

Target "EnsureDependencies" (fun _ -&gt;
    ensureToolIsDownloaded "dotCover" "https://YourLocalDotCoverDownloadUrl/dotCoverConsoleRunner.2.6.608.466.zip"
    &lt;code&gt;RestorePackageId (fun p -&gt; { p with OutputPath = "tools"; ExcludeVersion = true; Version = Some (new Version("2.6.3")) }) "NUnit.Runners"
</pre>

**Generating the coverage reports**

Next up is creating a target to actually run our tests and generate the coverage reports. We&#8217;re using the [DotCover extensions](http://fsharp.github.io/FAKE/apidocs/fake-dotcover.html) in F# Make that I contributed a little while back. As mentioned, we&#8217;re using NUnit and MSpec which adds a little more complexity &#8211; as we must generate each coverage file separately, and then combine them.

<pre class="brush: fsharp; title: ; notranslate" title="">Target "TestCoverage" (fun _ -&gt;

  let filters = "-:*.Tests;" # exclude test assemblies from coverage stats
  # run NUnit tests via dotCover
  !! testAssemblies
      |&gt; DotCoverNUnit (fun p -&gt; { p with
                                      Output = artifactsDir @@ "NUnitDotCover.snapshot"
                                      Filters = filters }) nunitOptions
  # run the MSpec tests via dotCover
  !! testAssemblies
      |&gt; DotCoverMSpec (fun p -&gt; { p with
                                      Output = artifactsDir @@ "MSpecDotCover.snapshot"
                                      Filters = filters }) mSpecOptions
  # merge the code coverage files
  DotCoverMerge (fun p -&gt; { p with
                                Source = [artifactsDir @@ "NUnitDotCover.snapshot";artifactsDir @@ "MSpecDotCover.snapshot"]
                                Output = artifactsDir @@ "DotCover.snapshot" })
  # generate a HTML report
  # you could also generate other report types here (such as NDepend)
  DotCoverReport (fun p -&gt; { p with
                                Source = artifactsDir @@ "DotCover.snapshot"
                                Output = artifactsDir @@ "DotCover.htm"
                                ReportType = DotCoverReportType.Html })
)
</pre>

All that&#8217;s left is to define the dependency hierarchy in F# make:

<pre class="brush: fsharp; title: ; notranslate" title="">"EnsureDependencies"
==&gt; "TestCoverage"</pre>

And off you go &#8211; calling your build script with the &#8220;TestCoverage&#8221; target should run all your tests and generate the coverage reports.