---
id: 654
date: 2018-07-14T22:28:11+00:00
author: james.crowley
layout: revision
guid: https://www.jamescrowley.net/2018/07/14/653-revision-v1/
permalink: /2018/07/14/653-revision-v1/
---
<p id="b0ed" class="graf graf--p graf-after--h3">
  <strong class="markup--strong markup--p-strong">… and regardless of cloud provider, it’s (probably) costing you 2x what it would on dedicated kit. So AWS could be costing you 4x what it would cost to license on dedicated hardware.</strong>
</p>

<p id="f9f4" class="graf graf--p graf-after--p">
  <strong class="markup--strong markup--p-strong"><em class="markup--em markup--p-em">Disclaimer:</em></strong><em class="markup--em markup--p-em"> I am certainly not a SQL Server licensing expert, nor that much of a cloud expert. The purpose of this post is to hopefully prove that I am, in fact, wrong. Please help with this!</em>
</p>

<p id="62c4" class="graf graf--p graf-after--p">
  Anyone that’s ever head to deal with SQL Server licensing (or indeed any kind of Microsoft licensing) knows what a minefield it is. In the public cloud, all your worries go away (ahem) and you can just wrap the license fee in the monthly cost via the hosting providers “service provider license agreement” with Microsoft.
</p>

<p id="f439" class="graf graf--p graf-after--p">
  Looking into it further, however, I realised there are actually big discrepancies between how much the different cloud providers charged you for licensing SQL Server itself.
</p>

<p id="e59d" class="graf graf--p graf-after--p">
  <strong class="markup--strong markup--p-strong"><em class="markup--em markup--p-em">Microsoft Azure: </em></strong>$75–80/core/month<br /> <strong class="markup--strong markup--p-strong"><em class="markup--em markup--p-em">Rackspace Cloud: </em></strong>around $100/core/month<br /> <strong class="markup--strong markup--p-strong"><em class="markup--em markup--p-em">AWS: </em></strong>anything from $136 to a $236/core/month
</p>

<p id="fc43" class="graf graf--p graf-after--p">
  <strong class="markup--strong markup--p-strong">In other words, your costs to license SQL Server could be 2x higher than Azure or Rackspace Cloud, and 4x higher moving to AWS cloud from dedicated hosting</strong> (see dedicated vs cloud)<strong class="markup--strong markup--p-strong">.</strong>
</p>

<p id="fc48" class="graf graf--p graf-after--p">
  At the moment, I have no good explanation for this. I’m still waiting for a response from AWS, and I’m hoping that I’m missing something here.
</p>

<p id="a93e" class="graf graf--p graf-after--p">
  <em class="markup--em markup--p-em">To head off the obvious — I realise there are databases with more cloud-friendly licensing. Unfortunately real life gets in the way, so we’re stuck with SQL Server until we can migrate away from it.</em>
</p>

### AWS, Rackspace Cloud & Azure compared {#c91f.graf.graf--h3.graf-after--p}

<p id="49aa" class="graf graf--p graf-after--h3">
  If you’ve got this far, I’m assuming you want some detail, so here goes.
</p>

<p id="87cb" class="graf graf--p graf-after--p">
  <strong class="markup--strong markup--p-strong">Assumptions</strong>
</p>

<ul class="postList">
  <li id="e591" class="graf graf--li graf-after--p">
    I am ignoring platform as a service offerings entirely (so SQL Azure, RDS you’re out)
  </li>
  <li id="0132" class="graf graf--li graf-after--li">
    I am only looking at SQL Server Standard
  </li>
  <li id="1ef9" class="graf graf--li graf-after--li">
    I am <strong class="markup--strong markup--li-strong">only</strong> considering SQL licensing costs, not the cost of the underlying hardware. For cloud providers that don’t explicitly state a licensing cost (only Azure does this), I’m going to take the cost of a Windows VM with SQL Server on it and subtract the cost of a Windows VM.
  </li>
  <li id="188d" class="graf graf--li graf-after--li">
    SQL Server is licensed per core, so I’m going to group these calculations by core
  </li>
  <li id="b723" class="graf graf--li graf-after--li">
    I’m just looking at monthly pricing with no commitment, as that’s how we’re currently paying for our dedicated kit too.
  </li>
  <li id="3f53" class="graf graf--li graf-after--li">
    Rackspace has different UK pricing in GBP, but I’ve spot checked the numbers and there was no material difference from their USD pricing, so we’re just going with that.
  </li>
  <li id="1a4d" class="graf graf--li graf-after--li">
    I am looking at prices for Europe-based data centres, in USD. I haven’t checked but I don’t expect this varies significantly from the US
  </li>
  <li id="4e3a" class="graf graf--li graf-after--li">
    I am assuming 730 hours in a month
  </li>
</ul>

<p id="db4e" class="graf graf--p graf-after--li">
  <strong class="markup--strong markup--p-strong">Data sources</strong>
</p>

<p id="76c9" class="graf graf--p graf-after--p">
  I have taken the prices from these locations: <a class="markup--anchor markup--p-anchor" href="http://www.rackspace.com/cloud/public-pricing" target="_blank" rel="nofollow noopener" data-href="http://www.rackspace.com/cloud/public-pricing">http://www.rackspace.com/cloud/public-pricing</a> <a class="markup--anchor markup--p-anchor" href="http://azure.microsoft.com/en-gb/pricing/details/virtual-machines/#Windows" target="_blank" rel="nofollow noopener" data-href="http://azure.microsoft.com/en-gb/pricing/details/virtual-machines/#Windows">http://azure.microsoft.com/en-gb/pricing/details/virtual-machines/#Windows</a>, <a class="markup--anchor markup--p-anchor" href="http://azure.microsoft.com/en-gb/pricing/details/virtual-machines/#Sql" target="_blank" rel="nofollow noopener" data-href="http://azure.microsoft.com/en-gb/pricing/details/virtual-machines/#Sql">http://azure.microsoft.com/en-gb/pricing/details/virtual-machines/#Sql</a>, and <a class="markup--anchor markup--p-anchor" href="http://aws.amazon.com/ec2/pricing/" target="_blank" rel="nofollow noopener" data-href="http://aws.amazon.com/ec2/pricing/">http://aws.amazon.com/ec2/pricing/</a> (as of 22 Jan 2015).
</p>

<p id="82b5" class="graf graf--p graf-after--p">
  Medium isn’t the best place for displaying tabular data, so I’ve included an image below, and you can download the <a class="markup--anchor markup--p-anchor" href="https://gist.github.com/jamescrowley/123673cb660eda1a7afe" target="_blank" rel="nofollow noopener" data-href="https://gist.github.com/jamescrowley/123673cb660eda1a7afe">spreadsheet here</a>.
</p><figure id="4fcc" class="graf graf--figure graf-after--p"> 

<div class="aspectRatioPlaceholder is-locked">
  <div class="aspectRatioPlaceholder-fill">
  </div>
  
  <div class="progressiveMedia js-progressiveMedia graf-image is-canvasLoaded is-imageLoaded" data-image-id="1*gaTZpgOAnFEItTC5lb5AhA.png" data-width="1652" data-height="1042" data-action="zoom" data-action-value="1*gaTZpgOAnFEItTC5lb5AhA.png" data-scroll="native">
  </div>
</div></figure> <figure id="4fcc" class="graf graf--figure graf-after--p"> 

<div class="aspectRatioPlaceholder is-locked">
  <div class="progressiveMedia js-progressiveMedia graf-image is-canvasLoaded is-imageLoaded" data-image-id="1*gaTZpgOAnFEItTC5lb5AhA.png" data-width="1652" data-height="1042" data-action="zoom" data-action-value="1*gaTZpgOAnFEItTC5lb5AhA.png" data-scroll="native">
    <img class="progressiveMedia-image js-progressiveMedia-image" src="https://cdn-images-1.medium.com/max/800/1*gaTZpgOAnFEItTC5lb5AhA.png" data-src="https://cdn-images-1.medium.com/max/800/1*gaTZpgOAnFEItTC5lb5AhA.png" />
  </div>
</div></figure> 

<p id="8b0e" class="graf graf--p graf-after--figure">
  <strong class="markup--strong markup--p-strong">Azure</strong> is the cheapest, working out around $75–80 per core (regardless of VM, as they list their SQL prices separately). I’m assuming this is essentially cost price, as I’ve been told elsewhere that a 4 core license via the SPLA scheme is generally $300/month. Not surprising, given it’s Microsoft licensing their own product.
</p>

<p id="23b8" class="graf graf--p graf-after--p">
  <strong class="markup--strong markup--p-strong">Rackspace Cloud</strong> is more expensive, but very consistent, working out between $100 per core (regardless of VM), <strong class="markup--strong markup--p-strong">so a 25% markup</strong>. As a note of comparison, on our dedicated kit, we pay £420/month for a 6 core license, which works out at £70/$106 USD so this is consistent.
</p>

<p id="3d73" class="graf graf--p graf-after--p">
  <strong class="markup--strong markup--p-strong">AWS</strong> is the big surprise here, with prices ranging anything from $136 at it’s cheapest to $236 (m series followed by r series, followed by c series). <strong class="markup--strong markup--p-strong">That’s a whopping 70%-195% markup.</strong>
</p>

### Cloud vs Dedicated {#9648.graf.graf--h3.graf-after--p}

<p id="ae1b" class="graf graf--p graf-after--h3">
  I mentioned earlier another price differential moving to cloud. <strong class="markup--strong markup--p-strong">Regardless of which cloud provider you’re using, SQL Server will (probably) cost you 2x what you’re paying for dedicated kit.</strong> That’s because SQL Server licensing <a class="markup--anchor markup--p-anchor" href="http://www.google.co.uk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&sqi=2&ved=0CEAQFjAA&url=http%3A%2F%2Fdownload.microsoft.com%2Fdownload%2F6%2F6%2FF%2F66FF3259-1466-4BBA-A505-2E3DA5B2B1FA%2FSQL_Server_2014_Licensing_Datasheet.pdf&ei=3v7AVNOBJ5G5aY7ygagF&usg=AFQjCNEImWMnUJ4Ud7JH-jYZuwFDMVZo7g&sig2=Xv-SVRNVkHm7MKY1XUULTA&bvm=bv.83829542,d.d24" target="_blank" rel="nofollow noopener" data-href="http://www.google.co.uk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&sqi=2&ved=0CEAQFjAA&url=http%3A%2F%2Fdownload.microsoft.com%2Fdownload%2F6%2F6%2FF%2F66FF3259-1466-4BBA-A505-2E3DA5B2B1FA%2FSQL_Server_2014_Licensing_Datasheet.pdf&ei=3v7AVNOBJ5G5aY7ygagF&usg=AFQjCNEImWMnUJ4Ud7JH-jYZuwFDMVZo7g&sig2=Xv-SVRNVkHm7MKY1XUULTA&bvm=bv.83829542,d.d24">ignores hyper threaded cores</a> (PDF) on dedicated kit, but not on virtualized kit, assuming you are only licensing the VM and not the host itself. So if you’re running in a virtualized environment with hyperthreading turned on (Azure, AWS, Rackspace Cloud, for instance), you have two virtual cores for each real processing core, and so you’ll need to pay for twice the number of cores to get the (vaguely) equivalent performance.
</p>

### Wrapping up {#1e79.graf.graf--h3.graf-after--p}

<p id="ad04" class="graf graf--p graf-after--h3">
  Like I said at the start, I’m no licensing expert, but given the already painful SQL licensing costs, seeing the huge differences we’d be paying in AWS vs other cloud providers is a hard pill to swallow. <strong class="markup--strong markup--p-strong">Please let me know if you spot any mistakes in my calculations!</strong>
</p>

<p id="0022" class="graf graf--p graf-after--p graf--trailing">
  It would certainly push us away from SQL Server even faster than we were planning, but in the meantime… would be interested to hear any insights as to why there are such big variations and such a huge markup at AWS!
</p>