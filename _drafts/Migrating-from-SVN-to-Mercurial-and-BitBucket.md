---
id: 183
title: Migrating from SVN to Mercurial and BitBucket
date: 2011-05-04T20:32:33+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=183
permalink: /?p=183
categories:
  - Uncategorized
---
I few old projects are still lurking in the depths of SVN, so from time to time I need to remind myself how to bring them into the world of Mercurial! I use [bitbucket](http://www.bitbucket.org/) for our Mercurial hosting &#8211; reliable and free for up to 10 users, and the [TortoiseHg](http://tortoisehg.bitbucket.org/) tool for Windows Explorer integration.

Here goes for the steps to migrate:

  1. Install [TortoiseHg](http://tortoisehg.bitbucket.org/)
  2. Open TortoiseHg Workbench, select File | Settings, click &#8220;Extensions&#8221; from the list and check the box next to .Convert. This enables an extension to allow us to convert repositories.
  3. Create the

In SVN it&#8217;s fairly typical to have a single repository, with multiple projects within it, creating a structure like this:

project-a  
&#8212; branches  
&#8212;- 1.x  
&#8212;- feature-1  
&#8212; tags  
&#8212; trunk  
project-b  
&#8212; branches  
&#8212; tags  
&#8212;- 1.0  
&#8212;- 1.1  
&#8212; trunk

While SVN allows you to check out subdirectories of a repository, Mercurial does not &#8211; so instead it makes more sense to have one repository per project.

I decided to do each at a time (a la http://stackoverflow.com/questions/4210345/split-subversion-repository-into-multiple-mercurial-repositories) in order to correctly preserve tags/branches.

hg &#8211;config convert.svn.trunk=BlipNetwork/trunk &#8211;config convert.svn.branches=BlipNetwork/branches &#8211;config convert.svn.tags=BlipNetwork/tags convert &#8211;authors authors.txt &#8220;D:\local\path\to\svn\repository\main&#8221; NewHgRepoName

the [documentation](http://mercurial.selenic.com/wiki/ConvertExtension) describes further options such as renaming branches as you import, should you need it.

Watch in pain seeing all the terrible ancient comments going back to the year dot in the SVN repository. Oh sorry, maybe just me.

Next we update the local repository to the latest revision

cd NewHgRepoName  
hg update

Create your new repository on bitbucket, and then push your local copy up.

hg push https://bitbucket/newrepo

**Dealing with SVN externals**

Mercurial doesn&#8217;t have externals in quite the same way as SVN, but you can use it&#8217;s [subrepository](http://mercurial.selenic.com/wiki/Subrepository) feature to get a similar effect.

**Remembering passwords**

Enable keyring, then edit .hg/hgrc file (or mercurial.ini from the workbench app)

[auth]  
bitbucket.schemes = http https  
bitbucket.prefix = https://bitbucket.org/xyz  
bitbucket.username = xyz

this will reuse authentication across any repositories starting with that prefix.

**Continous integration**

TeamCity doesn&#8217;t currently support keyring, which means if you&#8217;re pulling subrepositories in addition to the primary repository, you&#8217;ll get stuck asking for passwords. The solution is to edit the .**hrc** file and do something.

The final gotcha for me was to add &#8220;-:/.hgtags&#8221; to the VCS checkout rules so that it doesn&#8217;t rebuild on the basis of it&#8217;s own tagging.