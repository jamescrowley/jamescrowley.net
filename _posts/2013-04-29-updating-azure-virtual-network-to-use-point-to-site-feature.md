---
id: 282
title: Updating Azure Virtual Network to use point-to-site feature
date: 2013-04-29T17:02:21+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=282
permalink: /2013/04/29/updating-azure-virtual-network-to-use-point-to-site-feature/
sfw_comment_form_password:
  - 17QgwyPNlEH5
categories:
  - DevOps
tags:
  - azure
---
Scott recently announced support for [point-to-site VPN connections into Azure](http://weblogs.asp.net/scottgu/archive/2013/04/26/windows-azure-improvements-to-virtual-networks-virtual-machines-cloud-services-and-a-new-ruby-sdk.aspx) &#8211; awesome! But what might not be so clear is how to enable it on your existing Virtual Network configuration &#8211; because you can&#8217;t make changes (at least through the UI) to your virtual network after it has been deployed and is in use.

Fortunately, there appears to be a workaround.

1) Export your existing configuration (go to the Networks view in the Azure management portal, and click the Export button).

2) Modify the XML with the following. Firstly, you need to add a new &#8220;GatewaySubnet&#8221; entry, which should be **inside** the address space of your virtual network. You then need to add a &#8220;VPNClientAddressPool&#8221; node, with an AddressPrefix **outside** the address space of your virtual network.

    <VirtualNetworkSite name="XNetwork" AffinityGroup="NorthEurope">
      <AddressSpace>
        <AddressPrefix>10.4.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        ...
    <strong>    <Subnet name="GatewaySubnet">
          <AddressPrefix>10.4.1.0/24</AddressPrefix></strong>
    <strong>    </Subnet></strong>
      </Subnets>
      <Gateway>
    <strong>    <VPNClientAddressPool> </strong>
    <strong>      <AddressPrefix>10.0.0.0/24</AddressPrefix> </strong>
    <strong>    </VPNClientAddressPool></strong>
        ...
      </Gateway>
    </VirtualNetworkSite>

3) Go to Networks | Virtual Network | Import Configuration and re-upload your XML file.

Sorted! Now you can continue to configure point-to-site connectivity from the network dashboard as [described here](http://msdn.microsoft.com/en-us/library/windowsazure/dn133792.aspx).