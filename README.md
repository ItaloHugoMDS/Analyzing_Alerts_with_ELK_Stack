# Analyzing Alerts with ELK Stack  

This project showcases the use of the Elastic Stack (ELK Stack) tools for verifying alert, analyzing logs and
investigating security events.  

In this activity, a scenario prompt was provided with some questions to be answered by analysing the data. The resources
used for this showcase, which include the prompt, questions, and data logs, were taken from the
[Cisco's CyberOps][link1] course provided by *Cisco's* and *NetaCad's* team. **The analysis and responses for the questions
are original and done by the owner of this repository.**  

The resources used during this analysis are:  

- [Security Onion (OS)][link2]
- [Sguil][link3]
- [Wireshark][link8]
- [Elastic Stack (*ELK Stack*)][link4]

---

# Index  

1. [Prompt][link5]
2. [Questions and Responses][link6]
3. [Analysis][link7]

---

# Prompt  

A computer got infected in March 2019. Network security tools generated alerts regarding this infection. A deep analysis
need to be performed to determine some details about this security incident.  

First, some details are provided about the incident:    

- The infection occurred within a Windows machine;  
- The infected machine was working within an *Active Directory* (AC) environment;  
- The incident occurred in March 2019 date range.  

---

# Questions and Answers  

The analysis will answer some questions about the security incident. These questions are essential part of the
investigation and will help to understand the scope of the problem.  

These are the questions:  

1. **What was the specific time of the attack in 2019-03-19?**  
2. **Which Windows host computer was infected?**  
3. **What was the computer infected with?**  

For the purpose of clarity, the answers will be provided here and further explained, as well as deeply analyzed, in the
[Analysis][link7] segment of this report.  

The answers for the previous questions are:  

1. What was the specific time of the attack in 2019-03-19? => **Started at 01:45 am and ended at 04:54 pm.**  
2. Which Windows host computer was infected? => **Hostname: BOB-TIGER-PC, Host Domain: littletigers.info.**  
3. What was the computer infected with? => **VBKryjector (Trojan)** and **Dridex (Info-stealer)**.  

---

# Analysis  

The first step for the analysis will focus on verifying alerts. [Sguil][link3] tool will be the starting point, from
there the research will be pivoted to other tools and areas for further inspection.  

![Sguil Alerts](./images/1-Sguil_Alerts.png "Sguil Alerts")   

The main alerts for the scope of this investigation are highlighted within the red rectangle.  

They will be investigated from bottom to top, since this is the order which the timestamps are listed.  

![Alert 1 - DNS](./images/2-Alert1_DNS.png "Alert 1 - DNS")  

The first alert is highlighted, some details need to be made clear:  

1. The alert has **5.439** as the **Alert ID**. This alert also has **DPort** (*Destination Port*) **53** which is a
common port associated with **DNS** protocol.  
2. This tab shows more details regarding the selected alert. It is possible to see that this alert was generated due to
the infringement of a rule regarding DNS.  

This rule prohibits any **External** IP address going to the **Internal** IP address on port **53** (*DNS protocol*).
Since the IP address **10.0.90.215** made a DNS query to the internal address **10.0.90.9**, a **policy violation**
alert was created.  

![Alert 1 - DNS - PCAP Analysis](./images/3-Alert1_DNS_PCAP.png "Alert 1 - DNS - PCAP Analysis")  

Analyzing traffic generated by the alert, the previous assessed information can be verified:  

1. The **source address** is **10.0.90.215**, which is an external address, queried the **10.0.90.9** **destination
address** on port **53** (*DNS protocol*).  
2. Analysing the data within the **application layer** (*using the TCP/IP model as a reference*), the DNS information
shows that it was **DNS Update** for the source address.  
3. Here it's possible to see the verification of one of the answers: *Which Windows host computer was infected?* => 
**Bobby-Tiger-PC** at the address **10.0.90.215**.  

All the other alerts involve this IP address. Therefore, it's possible to affirm this is the address of the infected 
host computer.  

Continuing the investigation by moving to different alerts.  

![Alert 2 - HTTP](./images/4-Alert2_HTTP.png "Alert 2 - HTTP")  

The highlighted alerts are going to be assessed as a single one. That is because this set of alerts have the same
**SCR IP** (*IP Source Address*) and **DST IP** (*IP Destination Address*), they also make use of equal **SPort**
(*Source Port*) and **DPort** (*Destination Port*).  

![Alert 2 - HTTP - Event 1](./images/4-Alert2_HTTP_Event1.png "Alert 2 - HTTP - Event 1")  

Details about the **Event 1**:  

- This alert was generated due to a **policy violation**, which can be seen within the details of the alert next to the
**classtype** info;
- The rule that triggered the alert detected a **Windows Executable file** within the transmitted data. This can also be
verified in the **msg** info section and **content** section as well.  

![Alert 2 - HTTP - Event 2](./images/4-Alert2_HTTP_Event2.png "Alert 2 - HTTP - Event 2")  

In **Event 2**, the alert was triggered for a similar reason as in **Event 1**. The rule identified a suspicious file
within the transmitted data which contained the *MZ* characters, this information will be further explained along the
analysis.  

![Alert 2 - HTTP - Event 3](./images/4-Alert2_HTTP_Event3.png "Alert 2 - HTTP - Event 3")  

**Event 3** has similar triggers as the previous ones. The new flag for malicious activity is contained in the
**classtype** section, which identified the same *.exe* file, but it flagged as **trojan-activity**.  

![Alert 2 - HTTP - Event 4](./images/4-Alert2_HTTP_Event4.png "Alert 2 - HTTP - Event 4")  

**Event 4** has all the previous indicators, such as the **MZ** characters in the transmitted data and the flag of
**trojan-activity** in the alert's details.  

![Alert 2 - HTTP - Transcript](./images/4-Alert2_HTTP_Transcript.png "Alert 2 - HTTP - Transcript")  

All the previous events have the same transcripts of the network traffic which present the following **IoCs**
(*Indicators of Compromise*).  

1. The **Source IP Address**, which is the address of the Windows Host;  
2. **Destination IP Address**, this is the address of the server that is hosting the downloaded file;  
3. Here it's **HTTP GET** performed by the source IP address. In fact, the file requested was a Windows executable named
**"test1.exe"**;  
4. Other important details, which can be further used as evidence for the investigation, are the information about the
server that is hosting this payload. The server is running in an instance of **CentOS** (a unix based OS) and making use
of **Apache** version 2.4.6;  
5. Even though the HTTP GET requested a specific *.exe* file, this item confirms that this transmission contains a
Windows Executable.  

When analyzing the metadata, a Windows Executable file starts with the **MZ** characters. EXE files also have the
string: "*This file cannot be run in DOS mode*".  

Other events present the exact same information as previously seen transcript. They are the following:  

![Alerts 3 - HTTP](./images/5-Alert3_HTTP.png "Alerts 3 - HTTP")  

Those events share the same information as the previous ones. The same **IP source** and **destination addresses** and
**source** and **destination ports**.  

![Alert 3 - HTTP - Event 5 - Part 1](./images/5-Alert3_HTTP_Event1_Part1.png "Alert 3 - HTTP - Event 5 - Part 1")  

Just as the previous events, **Event 5** shows, in the details section, that the rules identified the *.exe* file, which
can be verified next to the **content** info.  

![Alert 3 - HTTP - Event 5 - Part 2](./images/5-Alert3_HTTP_Event1_Part2.png "Alert 3 - HTTP - Event 5 - Part 2")  

Still in the details of **Event 5**, the **classtype** was flagged as a **trojan-activity**. The transmission was also
labeled as a "**malware family MalDocGeneric**", which is the rule's way of saying that this file is associated with
malware activity.  

![Alert 3 - HTTP - Event 6](./images/5-Alert3_HTTP_Event2.png "Alert 3 - HTTP - Event 6")  

**Event 6** presents the same previously seen flags and labels.  

![Alert 3 - HTTP - Transcript](./images/5-Alert3_HTTP_Event2_Transcript.png "Alert 3 - HTTP - Transcript")  

Just as the previous network traffic transcript, **Events 5** and **6** display the same information. The most important
ones are:  

1. The same **destination address**, which points to the server hosting the "**test1.exe**" file;  
2. The same information about the hosting server. The same **OS** and **web server** software;  
3. Just as the previous traffic, the data contains the same indicators of the Windows Executable file.  

An executable file being downloaded over the internet is not something unusual. Even though this action can violate
company's policies, the download of a file by itself does not classify as malicious event.  

In order to classify this as a malicious event, further investigation needs to be conducted on the downloaded file.  

![Alert 3 - Wireshark Traffic](./images/6-Alert3_Wireshark_Traffic.png "Alert 3 - Wireshark Traffic")  

Pivoting to the traffic analysis tool [Wireshark][link8], an object could be extracted from the traffic which is the
file contained in the alerts: "**test1.exe**".  

![Alert 1-6 - File Info](./images/7-Alert3_File1_Info.png "Alert 1-6 - File Info")  

By extracting the file from the previously seen pcap, some information can be determined about the file:  

1. The file is in fact a Windows executable;  
2. The SHA256 hash of the file, which is the fingerprint of this executable.  

The SHA256 hash is going to be used to further investigate the origin and intentions of this file.  

The first resource that is going to be used to investigate the file's origin is [VirusTotal][link9]. A resource which
provide information about known malicious files, websites, domains, IPs, etc.  

![Alert 1-6 - File Verification](./images/8-Alert3_File1_Evaluation.png "Alert 1-6 - File Verification")  

Searching the file's hash in VirusTotal, the website returns many different indications that this file is malign in
origin. Some of those indications are listed as follows:  

1. File's hash => **2a9b0ed40f1f0bc0c13ff35d304689e9cadd633781cbcad1c2d2b92ced3f1c85**;  
2. Most common name for the file => **WEXTRACT.EXE**;  
3. The labels assigned to this file by the different vendors => **VBKryjector**;   
4. The category of the file => **Trojan**;  
5. Family which the file is associated => **VBKryjector**;  
6. Different labels set by the vendors.  



---

[link1]: https://www.netacad.com/courses/cybersecurity/cyberops-associate
[link2]: https://securityonionsolutions.com/
[link3]: https://bammv.github.io/sguil/index.html
[link4]: https://www.elastic.co/elastic-stack
[link5]: https://github.com/ItaloHugoMDS/Analyzing_Alerts_with_ELK_Stack#prompt
[link6]: https://github.com/ItaloHugoMDS/Analyzing_Alerts_with_ELK_Stack#questions-and-answers
[link7]: https://github.com/ItaloHugoMDS/Analyzing_Alerts_with_ELK_Stack#analysis
[link8]: https://www.wireshark.org/
[link9]: https://www.virustotal.com/
