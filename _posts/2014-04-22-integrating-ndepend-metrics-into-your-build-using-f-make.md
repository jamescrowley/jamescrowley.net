---
id: 368
title: 'Integrating NDepend metrics into your Build using F# Make &#038; TeamCity'
date: 2014-04-22T21:27:17+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=368
permalink: /2014/04/22/integrating-ndepend-metrics-into-your-build-using-f-make/
categories:
  - Software Engineering
---
[NDepend](http://www.ndepend.com/) is an analysis tool giving you all kinds of code quality metrics, but also tools to drill down into dependencies, and query and enforce rules over your code base.

There&#8217;s a version that integrates with Visual Studio, but there&#8217;s also a version that runs on the console to generate static reports, and enforce any code rules you might have written.

I wanted to see how easy it would be to combine all of this and use NDepend to generate actionable reports and metrics on our code base &#8211; not just now, but how it shifts over time.

To do this, you need to

  1. Run your unit tests via a code coverage tool, such as dotCover. This has a command line version bundled with TeamCity which you are free to use directly.
  2. Run NDepend with your code coverage files and NDepend project file
  3. Store NDepend metrics from build-to-build so it can track trends over time

I&#8217;ve covered step 1 in my previous post on [generating coverage reports using dotCover](http://www.jamescrowley.co.uk/2014/04/22/code-coverage-using-dotcover-and-f-make/ "Code coverage using dotCover and F# make") . I recommend you read that first!

We can then extend this code to feed the code coverage output into NDepend.

**Downloading your dependencies**

I&#8217;ve already covered this in the [previous post I mentioned,](http://www.jamescrowley.co.uk/2014/04/22/code-coverage-using-dotcover-and-f-make/ "Code coverage using dotCover and F# make") so using the same helper method, we can also download our NDepend executables from a HTTP endpoint, and ensure we have the appropriate license key.

<pre class="brush: fsharp; title: ; notranslate" title="">&lt;pre&gt;Target "EnsureDependencies" (fun _ -&gt;
    ensureToolIsDownloaded "dotCover" "https://YourLocalDotCoverDownloadUrl/dotCoverConsoleRunner.2.6.608.466.zip"
    ensureToolIsDownloaded "nDepend" "https://YourLocalDotCoverDownloadUrl/NDepend_5.2.1.8320.zip"
    CopyFile (toolsDir @@ "ndepend" @@ "NDependProLicense.xml") (toolsDir @@ "NDependProLicense.xml")
)
</pre>

**Generating the NDepend coverage file**

Before running NDepend itself, we also need to generate a coverage file in the correct format. I&#8217;ve simply added another step to the &#8220;TestCoverage&#8221; target I defined previously:

<pre class="brush: fsharp; title: ; notranslate" title="">DotCoverReport (fun p -&gt; { p with
Source = artifactsDir @@ "DotCover.snapshot"
Output = artifactsDir @@ "DotCover.xml"
ReportType = DotCoverReportType.NDependXml })</pre>

**Generating the NDepend report**

Now we can go and run NDepend itself. You&#8217;ll need to have already generated a sensible NDepend project file, which means the command line arguments are pretty straightforward:

<pre class="brush: fsharp; title: ; notranslate" title="">Target "NDepend" (fun _ -&gt;

NDepend (fun p -&gt; { p with
ProjectFile = currentDirectory @@ "Rapptr.ndproj"
CoverageFiles = [artifactsDir @@ "DotCover.xml" ]
})
)</pre>

I&#8217;m using an extension I haven&#8217;t yet submitted to F# Make yet, but you can find [the code here](https://gist.github.com/jamescrowley/11194577), and just reference it at the top of your F# make script using

<pre class="brush: fsharp; title: ; notranslate" title="">#load @"ndepend.fsx"
open Fake.NDepend</pre>

After adding a dependency between the NDepend and EnsureDependencies target, then we&#8217;re all good to go!

**Recording NDepend trends using TeamCity  
** 

To take this one step further, and store historical trends with NDepend, we need to persist a metrics folder across analysis runs. This could be a shared network drive, but in our case we actually just &#8220;cheat&#8221; and use TeamCity&#8217;s artifacts mechanism.

Each time our build runs, we store the NDepend output as an artifact &#8211; and restore the artifacts from the previous successful build the next time we run. Before this was a bit of a pain but as of TeamCity 8.1 you can now [reference your own artifacts](http://youtrack.jetbrains.com/issue/TW-12984) to allow for incremental-style builds.

In our NDepend configuration in TeamCity, ensure the artifacts path (under general settings for that build configuration) includes the NDepend output. For instance

`artifacts/NDepend/** => NDepend.zip`

go to Dependencies, and add a new artifact dependency. Select the same configuration in the drop down (so it&#8217;s self referencing), and select &#8220;Last Finished Build&#8221;. Then add a rule to extract the artifacts and place them to the same location that NDepend will run in the build, for instance

`NDepend.zip!** => artifacts/NDepend`

**TeamCity Report tabs**

Finally, you can configure TeamCity to display an NDepend report tab for the build. Just go to &#8220;Report tabs&#8221; in the project (not build configuration) settings, and add NDepend using the start page &#8220;ndepend.zip!NDependReport.html&#8221; (for instance).

Hope that helps someone!