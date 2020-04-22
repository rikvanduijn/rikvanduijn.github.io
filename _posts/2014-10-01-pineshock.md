---
id: 9
title: PineShock Abusing Shellshock via a Pineapple
date: 2014-10-01T21:50:00+01:00
author: Rik
layout: post
permalink: /2014/10/01/combining-shellshock-with-my-wi-fi-pineapple/
---
Hi All,

This time I will write my post in English since it’s directed at security folks. I got the idea of chaining the karma module of the pineapple with shellshock on the day of the advisory. I was giving a demo with the pineaple and noticed allot of systems called “mbp-*” which I guess where mac book pro’s. At the end of the day Trustedsec released a POC with a DHCP server which got me thinking. Since OSX was vulnerable I wanted to see if I could force them to join my network and own all those shiny Macbooks.

UPDATE: The OSX DHCP client is not vulnerable, still a nice attack to demonstrate the risk of automatically joining networks.

One problem, I don’t own a Macbook but I do have a spare machine with Ubuntu and an outdated version of Bash.

The process:

1. Start karma module to force people to join the Wi-Fi.
2. Once the machine joins wait for a DHCP request and send them the malicious DHCP-offer.
3. Wait for a reverse shell and have fun!

The DHCP server of the Pineapple was disabled via:

uci set dhcp.lan.ignore=1

uci commit dhcp

/etc/init.d/dnsmasq restart

I used the metasploit module: exploit/unix/dhcp/bash_environment. Next I added an open Wi-Fi network to the Ubuntu machine. Since the machine was only used on secured networks I needed to add this. The standard Macbook is probably probing for various SSID’s without authentication.

<img src="/images/pina01.png" width="600">

After this the laptop automatically connected to the SSID, and a DCHP request was fired, the metasploit module sent a dhcp offer containing the same string in various DHCP options. The module uses the following options to deploy the payload:

* DOMAINNAME
* HOSTNAME
* URL

<img src="/images/pina02.png" width="600">


I noticed the Ubuntu machine does not trust one of the above options I think it was the domain name option but I am not sure. This means the other two DHCP options will be executed and we will receive two reverse shells once our payload is executed. The way the reverse shell is executed is via /etc/crontab, a new entry is added that needs to run immediately. First a sleep of a random duration then a telnet connection is set up to our machine on port 4444. After this the module takes over and cleans the crontab to ensure the machine does not keep connecting.

<img src="/images/pina03.png" width="600">


Nice this seems to be going well 🙂 let’s look at our shell one more time just because it’s that much fun!

<img src="/images/pina04.png" width="600">


The evil thing about this attack is the fact that the user has no choice but to join my network, vulnerable machine’s will get owned.

 

UPDATE: I just received news that the DHCP client voor OS X is not vulnerable to shellshock. Unfortunately I did not know this and did not have a mac to test on. It still is a nice attack because of the user being forced to join the network. It’s just not that prevalent:P

If you have any questions, find me on Twitter: Follow <a href="https://twitter.com/rikvduijn">@rikvduijn</a>

