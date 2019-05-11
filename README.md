
## 2018 Notes

I recently worked my way through Georgia Weidman's excellent book “Penetration Testing: A Hands-On Guide to Hacking.” This is the third introductory pentesting book I've read, and it's by far the best: it lays out wonderfully clear explanations about what's going on, and reinforces the material with fun, informative hands-on exercises. Aspiring pentesters, this is your book.

You can get "Penetration Testing" at [No Starch Press's website](https://nostarch.com/pentesting). Tip: Georgia Weidman has posted a generous coupon code for the book on her [Twitter bio page](https://twitter.com/georgiaweidman).

So why "2018 Notes"? "Penetration Testing" was published in June 2014, and, four years later, some things have changed. Websites with archives of vulnerable applications have vanished, it's not particularly easy to find Windows XP, the tooling changes. So this post is a collection of notes I've kept about what's changed, and what workarounds I've found as I went through the book. I hope these notes will be useful to other readers too. This is still a great course on pentesting, and it's completely worth investing a little time in workarounds to continue working through the book in 2018.

**UPDATE: The author has offered to help people working their way through the book to find the old versions of software used in her book. That's going to be a waaaaay easier path to follow than using my workarounds, so you should definitely take her up on her kind offer. Look at the pinned tweet on [Georgia Weidman's Twitter bio page](https://twitter.com/georgiaweidman) for the details. H/T to Joseph G. for pointing this out to me.**

P.S.: I understand that the author is working on a second edition of "Penetration Testing." Before we start, we should all get into meditative positions and send her our most awesomest positive book-writing vibes. I am visualizing: a second edition, full of new tasty morsels of pentesting goodness and enlightenment. And I am also visualizing: buying the second edition the instant it comes out. **UPDATE: The author has a Patreon page up at https://www.patreon.com/georgiaweidman to support her work on the second edition. I was very happy to sign up and make this happen sooner.**

## Before you start

The author has some book updates posted [here](http://bulbsecurity.com/products/penetration-testing-book/#1458760607758-532c63a6-96d7). There are some other updates on the [book’s No Starch Press website](https://nostarch.com/pentesting). Be sure to look these over.

## My test lab

I use an Intel NUC as my main desktop. So for my test lab I installed a new SSD with a fresh install of Linux Mint 18.3, and used VirtualBox for my test VMs. Each of the virtual machines is configured with internal networking only, in order to keep wayward packets out of the Internetosphere. I move files around from host to guest with shared folders.

I already have an Office365 subscription, so I did my notetaking with OneNote in a browser on my host. I copy/pasted and screenshotted my results into my host-side notes.

I view my host and guests as disposable scratch systems, so I didn't set up a backup system. If that's not true for you, you will want to get one set up to deal with [situations like the one Arjen Wiersma ran into with a wiper attack on his shared folders](https://www.buildfunthings.com/OSCP-Week-4/). The one exception was to make sure I had automatic OneNote backups happening on another machine.

## Vulnerable Software links

The download server for oldapps.com seems to have disappeared into the mists of time. Which means a fair number of links in the “Installing Vulnerable Software” section are bitrot.

I did find replacement links. Warning: I am in no way vouching for these websites, or the software packages. Don’t install these on a system that you care about, or one that is connected to the Intertubes. See the PSA in "My Test Lab" above.

My replacement links:

I found version 1.7.2 of XAMPP at: https://www.exploit-db.com/exploits/10391/.

I found version 8.1.2 of Adobe Acrobat Reader at ftp://ftp.adobe.com/pub/adobe/reader/win/8.x/8.1.2/enu/AdbeRdr812_en_US.exe.

I found version 5.55 of Winamp at: http://www.filehorse.com/download-winamp/4137/download/.

You can find the Java 7 Update 6 JRE, but first you'll have to sign up for an oracle.com account. Once you are signed into your account, go to the download page at http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html. Scroll down (a lot!) to get the install packages for the book-recommended Java 7 Update 6 package.

The location for the mona.py plugin for the Immunity Debugger has moved. It now lives on github, at https://github.com/corelan/mona.

## Test Lab VMs

For my Kali machine, I’m running the 64-bit 2017.3 version. The virtual machines images are on the Offensive Security site, instead of with the ISOs on kali.org. The link for the Kali VMs is [here](https://www.offensive-security.com/kali-linux-vmware-virtualbox-image-download/).

For my XP VM, I bought a full copy of Windows XP Pro + SP3, build 2002, from ebay and installed it in a VM. This is a version they sell for reburbishing older PCs. It cost $30. I got it from: https://www.ebay.com/usr/softeque. They delivered everything as promised. Full disclosure: I don't have any financial relationship with these folks. I'm just a happy customer.

I torrented the Ubuntu VM from the [No Starch website](https://nostarch.com/pentesting).

I got my copy of Windows 7 from the collection of VMs Microsoft makes available for browser compatibility testing. These VMs work for 90 days. The link is [here](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/).

## MS08-067

There are a number of places in Weidman’s book where she discusses the MS08-067 vulnerability in the Windows XP target. But I have a problem: Nessus doesn’t identify this vulnerability in its scan of my XPSP3-for-refurbs OS. And if you check the list of affected OSes for MS08-067 on Rapid7′s website (https://www.rapid7.com/db/modules/exploit/windows/smb/ms08_067_netapi), you’ll see that XP SP3 English isn’t on the list.

What Nessus does report is “Microsoft Windows SMBv1 Multiple Vulnerabilities,” with a “critical” level warning. If you read the details, it gives you a long list of CVEs, but says that there aren’t known exploits. Huh.

What I did instead was look into using some of the Eternal Blue exploits, aka MS17-010. I fired up Metasploit’s MS17-010 scanner, and it told me that my target was likely vulnerable. '<Rubs hands together!>' But when I read the info page for the exploit, I saw the supported targets were Windows 7 and Windows Server 2008 R2. '<Ceases rubbing hands together>'

I started searching other sites for MS17-010 exploit code. (This is a good time to remind you that I have not vetted any exploit code mentioned here. Caveat emptor! Here be dragons!! Etc.)

I found [Worawit’s MS17-010 exploit code](https://github.com/worawit/MS17-010). I first ran the checker.cy script against my target, and it reported that there were two named pipes available for anonymous use. Then I ran EternalBlue.py with the browser named pipe and saw it plant a pwned.txt file in my target’s C drive.  Whoo-hoo, your Equation Group tax dollars at work! :) Note: I did turn off the firewall on my XP SP3 machine, but that’s only modification I did to it. Everything else is what you get with a default install.

Now I decided to get a meterpreter shell going on my target. First I prepared a payload file:

    msfvenom –payload windows/meterpreter_bind_tcp RHOST=10.10.10.6 -f exe -o mp_bind.exe

Then I modified EternalBlue’s `smb_pwn()` function to comment out the pwned file copy, and to transfer my meterpreter payload file instead. Then I enabled a `service_exec()` call to execute the payload on the target.

In another metasploit shell on my attacker machine, I went into multi/handler and set the payload to windows/meterpreter_bind_tcp, and hit exploit to get the catching shell ready.

Then I ran my modified EternalBlue.py script and ran it. The payload file was copied and executed. In my other terminal window, the meterpreter shell popped open. A getuid command told me that I was NT Authority\SYSTEM. Whoop!

## More on SMB vulnerabilities

Page 146 of “Penetration Testing” mentions the nmap script `smb-check-vulns` and says it will test for MS08-067, among other vulnerabilities. But I’m running version 7.6 of nmap, and this `smb-check-vulns` has been refactored away.

Many SMB related scripts are in nmap’s scripting engine, though. You can find the complete list here: `https://nmap.org/nsedoc/`. There is a `smb-vuln-ms08-067` script, but the docs warn that it is unstable and can crash systems. Which seems less than stealthy.

## Msfvenom and PHP payloads

“Exploiting WebDAV Default Credentials” (page 182), describes exploiting a vulnerability in WebDAV to upload a PHP file with a meterpreter payload to a folder in the web server root, and then using a browser (or wget) to execute the payload and get a shell on the target. Pretty slick.

But I couldn’t get it to work, receiving a message from the handler that the session is not valid and that it will close. Several other people have had the [same issue](https://github.com/rapid7/metasploit-framework/issues/9345). 

The non-meterpreter PHP payload example that follows works fine. That rules out networking issues or the PHP config in the webserver. Is it the PHP meterpreter payload that msfvenom produces that’s the problem?

I don’t have any PHP-fu to draw on, so I worked around the whole thing by uploading a meterpreter .exe file in cgi-bin directory, and then executing it with wget, popping my sought-after meterpreter shell. I declared victory.

My msfvenom doesn’t have a version option, but I saw this with Metasploit version v4.16.28-dev.

## NFS

In “Exploiting Open NFS Shares” (page 194), when I tried mounting the Ubuntu target’s exported NFS file system, I got this error message: 

    root@kali:~# mount -o nolock 10.10.10.3:/export/georgia /tmp/foo
    mount: /tmp/foo: bad option; for several filesystems (e.g. nfs, cifs) you might need a /sbin/mount.<type> helper program.

Once I did an `apt-get install nfs-common` on my 2017.3 Kali machine, this problem went away.

## Bkhive/samdump2 -> pwdump

In “Recovering Password Hashes from a Windows SAM File” (page 204), the bkhive tool for extracting the bootkey from the SYSTEM file is discussed. Having this key allows you to then use samdump2 to extract the password hashes from the Windows SAM file.

2017.3 Kali doesn’t have the bkhive tool. Instead, it provides the pwdump tool. Pwdump also replaces samdump2′s functionality. Pwdump is part of the creddump package. You can find some documentation for the package [here](https://tools.kali.org/password-attacks/creddump).

## Passwords from Memory

In “Dumping Plaintext Passwords from Memory with Windows Credential Editor” (page 213), the book discusses grabbing unencrypted passwords for logged-in users out of the Local Security Authority Subsystem Service using the WCE tool. Now there’s an alternative tool: Mimikatz, written by Benjamin Delphy. You can find the code and documentation here: https://github.com/gentilkiwi/mimikatz.

Better yet, mimikatz is  built right into the meterpreter. Here’s an example:

    meterpreter > mimikatz_command -f privilege::debug
    Demande d'ACTIVATION du privil�ge : SeDebugPrivilege : OK
    meterpreter > mimikatz_command -f sekurlsa::logonPasswords
    “0;8240710”,“NTLM”,“georgia”,“XPTEST”,“lm{        e52cac67419a9a224a3b108f3fa6cb6d }, ntlm{ 8846f7eaee8fb117ad06bdd830b7586c }”
    password"
    "
    password"

## MS10_002_aurora

I tried to repro the MS10_002_aurora vulnerability (page 220) with Internet Explorer, but no joy. The exploit’s info page says that it only works reliably on Internet Explorer 6. My refurb edition of Windows XP SP3 comes with Internet Explorer 8.

## Java exploits report

So, yeah, Java. I tried out the JRE attacks described in the “Java Exploits” section (page 230), using my Win7 as the victim machine. I had some success and also some not-success.

With IE 11 and the unsigned payloads, I got a warning about how out-of-date my Java was, and I had to click the “run anyway” button. But then my meterpreter shell popped right open. It ran with IE11WIN7\IEUser’s privileges.

I couldn’t get IE11 to play nicely with the signed_applet exercise. On my attacker machine, I saw it send the jarfile over and that it was waiting for the user to click accept. But back on my victim machine, all I saw was the same out-of-date warning, then a “loading” page, but no signed applet security warning.

For those of you in the Intertubes saying, “But IE 11 is actually pretty recent! Doesn’t Win7 ship with IE 8?”: yes, you are correct. When I installed Win7, I didn’t disable the update mechanism quickly enough. Then I got lazy and didn’t reinstall the OS. I strongly suspect I would have had a higher lulz quotient on these exercises if I had IE 8 running. As I proceed into the Win7 section of the book, I may regret that decision enough to go do the reinstall.

Version 57 Firefox didn’t work with any of the exercises. According to `https://www.fxsitecompat.com/en-CA/docs/2016/plug-in-support-has-been-dropped-other-than-flash/`, plugin support for everything but Flash ended in Firefox 52.

## Winamp exploit FAIL

To do the client-side exploit described on page 237, you’ll need a copy of the winamp skin “Bento.” It didn’t come with my winamp install, and after a lot of spelunking around on archive.org, I still couldn’t find it. I skipped the winamp exercise.

## AV-resistant encodings

Today I moved on to “Getting Past an Antivirus Program” (page 263). Since VirusTotal is constantly being updated, this is where I expected the biggest difference between the book’s results and what I saw.

For most of the msfvenom encoding examples, I was flagged by about two thirds or so of the scanners. The custom version was flagged by 24 of the 67 scanners, but the Microsoft scanner was one of them.

For my Hyperion test, I installed version 1.2 from `http://nullsecurity.net/tools/binary.html`. I got some error messages from Wine about missing DLLs. The fix is described [here](https://fedoraproject.org/wiki/MinGW/Configure_wine). The exact paths in the Configure_wine documents didn’t work for me; I had to run a `locate` on the DLLs and then append those paths.

Anyway, once I got my Wine troubles squared away, I encrypted my payload with Hyperion. But the scanners flagged the executable 47 of 67 times. So the scanners have greatly upped their Hyperion detection game since 2014. Worse yet, Microsoft’s scanner was one of those that flagged the executable.

Lastly, I installed Veil 3.0, available from `https://github.com/Veil-Framework/Veil`. Happily, the Veil executable snuck by the Microsoft Security Essentials scanner (with updated definitions) on my 32-bit Win7 SP1 VM. Way to go Veil!

Note about the Veil executable: I didn’t check it with Virus Total. The 3.0 version doesn’t ask you to refrain from testing it there, but the version used in the book did. I honored the book's request.

## MS11_080 Not Happening

In “Local Escalation Module for Windows” (page 284), the reader is walked through MS11_080, a Windows privilege elevation exploit, on the XP target.

The step failed for me, and a quick look at the add/remove programs dialog with the “show updates” checkbox checked shows why. KB2592799 was installed, which patches MS11_080.

## Faking an SMB Server to Get Hashes

In the “SMB Capture” section (page 302), the book shows one way to grabbing domain user hashes when you don’t have access to the domain controller, but you do have a shell with that user’s privileges on a non-domain controller (e.g., through token impersonation).

To do this, you set up a fake SMB server using metasploit’s auxiliary/server/capture/smb, and then use your shell to fire off a net use command to some nonexistent SMB share on your attacker machine. Metasploit with then (theoretically) record the hashes, which you can then try to crack.

The author notes that this exercise can be flaky – returning an “empty hash” message – since this is not happening on an actual Windows domain. And indeed I got this message when I tried it from my Windows XP machine. However, I tried it from my Windows 7 box, and it worked great.

## VBox Networking Setup for Pivoting

In “Pivoting” (page 304), the book looks at detecting and attacking hosts that you can’t get to directly, but that are accessible via a host you have already compromised. It talks you through the networking setup for this exercise assuming you are using VMWare VMs. I’m using VirtualBox, so here’s how I configured my VMs for these exercises:

My Kali, WinXP, Weidman Ubuntu, and Win7 machines are all set to use the default internal network. This means that all these VMs can talk to each other, but not my VM host or the outside world. In my case, this gave everybody 10.10.10.x IP addresses. This setup is through the VirtualBox Manager, and then the Settings tabs for each VM.

For the pivot exercise, I added a second internal network (”lateral”) for the Win7 and WinXP VMs. In my case, this gave those two VMs an additional IP address in the 169.254.x.x subnet. Then I went into the WinXP VM OS and disabled my first 10.10.10.x LAN connection. Now I was set up for the pivoting exercises.

## Pivoting With External Tools Over Meterpreter Sessions

In “Pivoting” (page 304), the book walks you through scanning and exploiting hosts on networks that aren’t directly accessible to your attack machine, and without having to move your tools over to your intermediate foothold machines.

Instead of using the “route” metasploit command, I used the “autoroute” meterpreter script to set up the tunnel. This worked great.

I ran the metasploit portscanner module against my one-hop-away target XP host. This worked great.

Then I set up the socks proxy and proxychains config to use an external tool over the metasploit tunnel, and here I ran into problems. I could do things like wget, but when I tried to use it with nmap, it came back saying all ports were closed. Huh.

The Interwebs are full of people saying that proxychains and nmap don’t work well together. I was finally able to get it to work by turning off the initial UDP-based ping (as suggested in the book) and also asking it to do a full connect scan (-sT). Of course, that’s going to leave droppings in the target’s logs. This was with version 3.1 of proxychains.

Finally I turned to exploiting the target XP machine over the meterpreter tunnel setup. Since the XP machine I’m working with isn’t vulnerable to MS08_067, I went back to the Python Eternal Blue exploit I used earlier. Reverse_tcp payloads weren’t going to work with the tunnel, so I changed its payload to a meterpreter_bind_tcp payload exe (the inline version, not the staged version) and delivered and executed it via the Eternal Blue script. Back on my target machine, I fired up a multi/handler and set the payload to windows/meterpreter_bind_tcp and hit run to connect ... and nothing happened.

Whoops, it turned out I was running the handler in a different instance of msfconsole from where I’d done the autoroute. I fired up the multi/handler in the same instance, and now I had a SYSTEM shell. Nice.

## Reflected XSS, foiled

In “Checking for a Reflected XSS Vulnerability” (page 330), the book describes a simple test for injecting code into an unsanitized user input box. It notes that some browsers have built-in XSS detection and remediation capabilities.

For all the browsers I tested (IE8, IE11, and FF52), this was the case. No alert box fun for me.

## w3af MIA

In “Web Application Scanning with w3af” (page 335), the book discusses the use of w3af for website vulnerability scanning.

Unfortunately, w3af isn’t available in Kali 2017.3, due to package dependency issues. [This ticket](https://github.com/andresriancho/w3af/issues/14982) describes the problem.

According to the w3af install docs at `http://docs.w3af.org/en/latest/install.html`, you should be able to just apt-get the w3af package, but it’s not in the Kali repo. The docs also describe how to get it working in Docker by cloning the github repo. If I had the will to continue hunting this down, then that is what I would try next. But I know exploit writing's next, and I move on.

## Exploit writing

Part IV, “Exploit Development” (page 359), is the super-duper fun stuff. This is where pentesting really came alive for me. I can't emphasize enough what a great job Georgia Weidman does in explaining all the moving parts.

Chapter 16 is about stack-based buffer overflows in Linux. Chapter 17 is the same topic, but for Windows. Msfvenom generated somewhat different payloads for me than the code used in the book, but with minor adjustments, this all went smoothly for me.

Chapter 18 covers SEH exploits. Alas, this part didn’t go so well. 

The book version harvested its pop pop ret code from version 4.2 of MFC42.DLL, which has SafeSEH turned off. With my XP SP3 version, I have version 6.2 of MFC42.DLL, which has SafeSEH turned on. In fact, the only part of the executable and its modules that has SafeSEH turned off is war-ftpd.exe itself.The exe’s base address is 0x00400000 and its top address is 0x00464000. So while there are plenty pop pop ret candidates in the exe, every last one of them will have a \x00 in them. Null bytes, strcpy breakage, no fun.

If I want to practice an SEH attack, I’m going to have to find another executable to work with. :(
