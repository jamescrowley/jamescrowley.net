---
id: 365
title: Applying app.config transformations (in the same way as web.config)
date: 2014-03-07T23:58:58+00:00
author: james.crowley
layout: revision
guid: http://www.jamescrowley.co.uk/2014/03/07/96-revision-v1/
permalink: /2014/03/07/96-revision-v1/
---
Visual Studio 2010 doesn&#8217;t have the same support for app.config files in the way that their web projects do, in order to vary connection strings and other configuration settings for different release modes &#8211; a real shame. [You can vote on the issue here](https://connect.microsoft.com/VisualStudio/feedback/details/564414?wa=wsignin1.0). In the meantime though, the ASP.NET team have a fix, [detailed here](http://blogs.msdn.com/b/webdevtools/archive/2010/11/17/xdt-web-config-transforms-in-non-web-projects.aspx).

All you need to do is save their [custom targets file](http://sedotech.com/Content/samples/TransformFiles.targets), add an imports tag immediately before the closing </Project> tag:

      ...
      <Import Project="$(MSBuildExtensionsPath)\Custom\TransformFiles.targets" />
    </Project>
    

And add a TransformOnBuild metadata property to each config file you want transformed. So

    <None Include="app.config" />

becomes

    <None Include="app.config">
      <TransformOnBuild>true</TransformOnBuild>
    </None>

(note you don&#8217;t need to do this on the configuration specific config files such as app.release.config). Then you can write your app.Release.config and similar files in the same way you do for web.config files. Sweet!