---
id: 9
title: PowerShell Better phishing for all!
date: 2015-02-15T21:50:00+01:00
author: Rik
layout: post
permalink: /2015/02/15/powershell-pentesting/
---

<br><br>
A year ago i was watching a presentation by Dave Kennedy (ReL1k) and Josh Kelly called: <a href="https://www.youtube.com/watch?v=JKlVONfD53w">“PowerShell…omfg”</a> the presentation shows multiple techniques that are very very useful during a pentest. After viewing the video I realized i could make a small addition to a phishing attack I use the pretext is simple: I e-mail a client an office document containing very important data they should NOT have received. People are very curious and tend to open files containing data they should not have received. The office document is protected using my encryption preparation utility (Patent pending).

<img src="/images/power01.png" width="600">

In order to decrypt the data macro’s should be enabled. I think you are getting the point right around here, the macro downloads my own backdoor to %TEMP% and executes the file. There is a BIG downside to that approach: most anti-virus alert or block on files being executed from the %TEMP% folder. This does not mean it doesn’t work it just means there is a chance of failure. And with phishing you get one chance so it needs to count!

<img src="/images/power02.png" width="600">

Watching the presentation I got excited hearing that PowerShell is able to import functions from any DLL, this allows you to use functions like VirtualAlloc, memset and CreateThread. This will allow you to allocate executable memory, fill it with your program and execute it. This means that we can use a simple PowerShell script to inject any backdoor we like into memory just like syringe.c. The big difference is no compiled code, every Windows version above Vista has PowerShell built in. But there is more! PowerShell is usually allowed to execute because the administrators use it for their scripts so no worries about application-white listing. And best of all i haven’t had any anti-virus alarming my victims, ruining my phishing attack.

TrustedSec released a script called unicorn.py which does all of the heavy lifting for you it uses MetaSploit to generate a Meterpreter and prepares a PowerShell script that injects and executes the Meterpreter. It even generates a Metasploit script for you which sets up the payload handler. While this is very nice a user is not likely to trust a PowerShell script they are fare more accustomed to Office documents. VBA allows access to the windows shell via the Shell() function, from here we can call PowerShell in order to execute our Meterpreter. There are some things to work around though, one of the most important things is that VBScript has a maximum line length. I have modified the original unicorn.py script in order to output an entire macro.

<img src="/images/power03.png" width="600">


The full script is available from my github repository. If you are using this script against targets in remote environments I would advise on using the windows/meterpreter/reverse_https this makes detecting/inspecting your connection much harder. I am now using the script during my phishing expeditions 🙂 It works allot better than just dropping an executable.

Stealing Credentials
Another great example of the power of PowerShell was displayed by enigma0x3, he created a script that asks the user for his username and password. The great thing is it looks like the actual login screen Winddows uses (probably because it is). This attack can be used in the scenario discussed above but is also great in post exploitation Wesley Neelen created a Metasploit module for this. The first time I saw the script I thought this would make a nice addition to my meterpreter injection, because I have a shell let’s have the credentials aswell!

I modified the script by engima0x3 and added a function to send the credentials to my own server, this makes the script usable for my macro. The modified script is available from my GitHub repo.

<img src="/images/power04.png" width="600">


In order to get this into an easily handle-able format you can use the following one-liner to convert your PowerShell script to a base64 encoded string in order to exectue our script as a one-liner.

{% highlight python %}

import base64, sys; print base64.b64encode(open(sys.argv[1]).read().encode('utf_16_le'))

{% endhighlight  %}

The output can be executed using the PowerShell command:

{% highlight powershell %}

powershell -win hidden -enc base64encodedscript

{% endhighlight %}

This all adds up to the following macro:

<img src="/images/power05.png" width="600">


Now let’s add this to my Excel macro and we are ready for some phishing!

<img src="/images/power06.png" width="600">


That’s all folks! If you have any questions, find me on Twitter: Follow <a href="https://twitter.com/rikvduijn">@rikvduijn</a>