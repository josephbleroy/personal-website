---
author:
  name: "Joseph"
date: 2016-05-07
linktitle: Packet Capture Analysis of Ransomware
title: Packet Capture Analysis of Ransomware
type:
- post
- posts
weight: 10
series:
- 
aliases:
- 
---

In the 2016 Data Breach Investigation Report, Verizon discovered that "**ransomware** is on the rise". Malware crafted to encrypt the victim's drive content, in lieu of a hefty Bitcoin payment to unlock the protected files, **encompasses 39% of crimeware incidents in 2015**. Hackers primarily targeted the **public sector, manufacturing, and information industries**. What makes ransomware so easy to deploy is the minimal skill set required to craft an executable file masked as something else, such as a gif or flash object.

Verizon recommends taking the following steps to prevent ransomware from infecting your technology assets:

**1) Patch promptly:** Cybercriminals are
successfully exploiting known vulnerabilities; timely patching could block many attacks.

**2) Implement configuration change monitoring:**
Many attack methods can be easily detected by watching key indicators.

**3) Back up your systems regularly:** This will keep
your business running should any systems fall foul of ransomware.

**4) Capture data on attacks:** Examine the different
types of malware you’ve fallen foul of—and, if possible, the entry point. This gives you
intelligence on where to prioritize your efforts.

A very detailed and understandable explanation on how ransomware is delivered and remains hidden can be found at **[Heimdal Security's blog](https://heimdalsecurity.com/blog/10-reasons-why-your-traditional-antivirus-cant-detect-second-generation-malware/)**.

With that being said, I decided to go through a packet capture exercise. The entire process can be found below. Hopefully those interested can learn from my experience and I can continue to evolve my packet capture analysis process.

**Madison Square Garden Security Operations Center**<br>
A recent packet capture analysis revealed that some type of malicious activity took place on Madison Square Garden’s network. Snort and Suricata logs displayed some events which required further investigation.

It’s important to become familiar with the Snort and Suricata event terminology:

    ET: emerging threat; something that is a deviant from normal activity and raises a flag in an IDS/IPS, such as Snort.

    Neutrino Landing: the user will visit a malicious webpage where an embedded Flash object will run code, usually JavaScript, to scan the user’s system for vulnerabilities to exploit.

    Nemucod: a JavaScript trojan virus that allows remote files to be downloaded without the user’s consent.

    Cryptowall: a type of malicious code that encrypts the contents of a user’s hard drive, thus making the files inaccessible. The only way to access the files against is by receiving the private key from the attacker, usually by paying a significant amount of money in the form of Bitcoin. This is usually labeled as “Ransomware”.

    AlphaCrypt: a type of ransomware that encrypts the contents of your hard drive then directs you to a page to pay Bitcoin to decrypt your files.


There are three hosts which reside on the internal network with the following attributes:

    Host 1: 192.168.122.52
    Host Name: Jennifer-PC
    MAC Address: 00:26:55:32:A9:17 (Hewlett Packard)

    Host 2: 192.168.122.130
    Host Name: FULL-METAL-JACK
    MAC Address: 00:22:19:E2:4B:86 (Dell Inc.)

    Host 3: 192.168.122.132
    Host Name: Hokaydoo-PC
    MAC Address: 48:5B:39:C1:F2:48 (ASUSTek Computer Inc.)

**Host: Jennifer-PC**<br>
Let’s take a look at some of the suspicious host activity. We’ll start with **Jennifer-PC**, **192.168.122.52**. Using Xplico, a PCAP analysis tool, I was able to get a high level overview of the number of DNS queries Jennifer-PC made. Most of the connections made were to valid websites, such as Google, Yahoo, and various CDN providers. However, a few hosts required further investigation as they were unknown to me.

    outdoorsamoa[.]com: 54.66.231.10
    st[.]domandvilma[.]com: 192.185.21.183

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image02.png)
![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image29.png)
![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image19.png)
![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image08.png)

The first connection Jennifer-PC made was to 54.66.231.10. I used a filter to check the packets sent between the two hosts: `ip.src==192.168.122.52 and ip.dst==54.66.231.10`

Nothing seemed abnormal and I ran a scan against the domain, **outdoorsamoa[.]com**, using VirusTotal. As expected, the domain came back clean. The second connection was to **st[.]domandvilma.com**: `ip.src==192.168.122.52 and ip.dst==192.185.21.183`. There were a lot less connections between the two hosts. While none of the TCP stream seemed alarming, running the domain **st[.]domandvilma[.]com/fvviewforumqn.php** in VirusTotal detected the domain as a malicious malware site.

The frame is 9983, so I wanted to export the packet as a HTTP object to investigate the website’s code. For this I used NetworkMiner to investigate the **fviewforumqn.php** file. To my surprise, the extension was actually a **.javascript** file, not a **.php** file.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image09.png)

While the activity is suspicious, investigating the actual contents of the file revealed the file to be non-malicious.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image25.png)

No further connections were made and my conclusion was that the payload was non-malicious, just odd.

**Host: Hokaydoo-PC**<br>
The next host, **Hokaydoo-PC** at **192.168.122.132**, displayed some malicious behavior. The Suricata event logs showed the following:

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image28.png)

From this Suricata event log we can see that the activity originated from **92.51.131.150** (**koeppl[.]com**) and was routed by **Hokaydoo-PC** at **192.168.122.132**. This type of activity is suspicious and requires further packet analysis in Wireshark: `ip.src==92.51.131.150 and ip.dst==192.168.122.132`. Following the TCP stream I determined the initial compromise took place when **Hokaydoo-PC** visited **google.com.au** and searched for **koeppl[.]com**. Nothing seemed too strange until I opened the files between the two hosts in NetworkMiner.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image07.png)

I decided to view the contents of **index.html**, and to my surprise I found a very strange embedded JavaScript code in the contents of the HTML document.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image21.png)

I knew this was not normal, so I decided to take a look on VirusTotal for the domain **koeppl[.]com**. The domain is part of a malware domain blacklist and is known as a malicious website. I decided that I wanted to know what the JavaScript actually contained to understand how it was working. To do this I used **[JSDetox](https://github.com/svent/jsdetox)**, an open source JavaScript malware analysis tool.

There were 4 malicious JavaScripts on the HTML page, however, I was not able to properly interpret the actual contents. I suspect I need some more practice with malware reverse engineering. The best tool I found to handle the task was **[Malware Jail](https://github.com/HynekPetrak/malware-jail)**, but once again my ability to reverse engineer the JavaScript malware was unsuccessful.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image14.png)

It’s to my understanding that **koeppl[.]com** infected the requesting host, **Hokaydoo-PC**, because the next request on the Suricata event log states **Neutrino EK Payload from 89.38.144.75**. This is suspicious because the IP address resolves to **uacltr[.]securetoppc[.]top** and **gbesbsdsb[.]securetopc[.]top**.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image13.png)
![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image04.png)

The domains are not only suspicious but the payloads redirect the host to some very suspicious files, as seen above. Let’s take a look at the actual files in NetworkMiner. The following files are suspicious and required further analysis on VirusTotal:

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/table.png)

The preceding HTML documents are all vulnerable as they all point back to **cheerful-stumble-broad.html.swf**, which is a known malware. When I opened **fellow-touch-death**, it actually deleted its contents and renamed itself to **fellow-touch-death-c.html**. The last URI was not a HTML file because upon opening it, the file contained unreadable data, possibly encrypted.

From what I’ve concluded, the initial infection came from visiting **koeppl[.]com**, at frame 12754, and at frame 12888 the infected SWF file was executed on the host’s computer:

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image11.png)
![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image10.png)

Let’s recap on what’s happened so far:

    1) Hokaydoo-PC visited koeppl.com
    2) The embedded JavScript requests *.securetopc[.]top
    3) SWF is downloaded
    4) Hokaydoo-PC is infected
    5) Hokaydoo is redirected to 188.138.101.154 (bizsouls[.]com)

I’m not sure what **bizsouls[.]com** is but VirusTotal says that it is clean. However, the following looks a bit suspicious.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image01.png)

There’s two PHP files with a few alphanumeric strings inside the contents of the files. Suricata logs say that it’s a Cryptowall check in.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image06.png)

I’ll assume that the values inside the two PHP files are passed to some JavaScript file which allows the redirection process to occur.

**Host: Full-Metal-Jack**<br>
The final host, **Full-Metal-Jack** at **192.168.122.130**, displayed some malicious behavior right off the bat. The Suricata event logs showed the following:

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image23.png)

Taking a look at the HTTP requests made by Full-Metal-Jack, shown below, I’ve determined that the initial compromise took place from an email link or attachment. This is due to the fact that the second item is mail.yahoo.com and is immediately followed by a malware domain, **ma-wt[.]com[.]sa**. I ran a quick check on VirusTotal to determine this information.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image20.png)

However, I wanted to know more. I followed the TCP stream starting at **ma-wt[.]com[.]sa**. It looks like **66b32.gif** was downloaded, which was probably the malware that infected the host.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image05.png)

To further validate this, I took at look at the anomalies section on NetworkMiner, and sure enough, there were multiple gif files that were unable to be correctly decoded.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image22.png)

It’s likely the malware was masked as a gif file, since running **66b32.gif** in VirusTotal displayed multiple detection warnings.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image16.png)

There are actually many naming convention variants for the gif file, such as **174125.gif**, but the SHA-256 value remains the same.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image15.png)
![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image12.png)

There was another gif file named **250acae.gif** that I also scanned and it also turned up as a positive malware on VirusTotal. After the initial website infected the host, several other websites sent malicious software to **Full-Metal-Jack**.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image03.png)

There were two additional IP addresses in the HTTP request stream for **Full-Metal-Jack**.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/table2.PNG)

The first host in the table above delivered the following “gif” payloads:

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image24.png)

Running both of the files through VirusTotal displayed various positive matches for malicious software. The second host, **freshanointingministries-sc[.]org**, delivered the following “gif” payloads as well:

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image17.png)

Once again, both files were malicious when uploading them to VirusTotal. Last but not least, let’s take a look at **Hokaydoo-PC** again.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image27.png)

We see that the host connected to **8.8.4.4**, which is a DNS server provided by Google. This isn’t abnormal, just Tor establishing a connection via a proxy. The real damage can be seen at frame 20250.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image00.png)

The SWF file that **Hokaydoo-PC** downloaded from koeppl.com encrypted his files and sent him to a ransomware page, **[3wzn5p2yium7akj].waytopaytosystem[.]com**:

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image18.png)

The ransomware page is shown below, **1f96s0p[1].html**, and **1f96s0p.html** takes the user to a basic ransomware page, which probably redirects to **1f96s0p[1].html**.

![](https://storage.googleapis.com/josephbleroy-images/images/2016/05/image26.png)

This is the full analysis of the PCAP file for the Madison Square Garden ransomware infection. It’s likely that the two hosts that became infected were either unlucky, such as **Hokaydoo-PC**, and **Full-Metal-Jack** was targeted in a phishing campaign to his Yahoo email account.

Since the users were all infected by visiting websites, I would recommend that they be wary of the links they click on or download from their email accounts. It’s also important to disable JavaScript, if possible, or block JavaScript that went through obscurification methods, such as the one found on koeppl.com.

I would also recommend that all software be kept up to date to prevent any vulnerability exploits, important data be kept on a share drive to prevent precious documents from being encrypted by hackers, and for training by security conscious employees be conducted to prevent this sort of event from happening.  

At the very least, I think having all browsers incorporate some type of blacklisting of malicious websites, or keep a proxy with a blacklist of host and IP addresses from accessing the malicious websites that delivered the SWF and fake gif files.

Overall, the main victims were **Hokaydoo-PC** and **Full-Metal-Jack**. However, **Jennifer-PC** did visit a malicious website, **st[.]domandvilma[.]com** where a JavaScript file was downloaded, the execution never actually did anything, that I was able to see.