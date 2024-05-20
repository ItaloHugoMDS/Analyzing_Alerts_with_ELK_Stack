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

Here will come a detailed analysis of the data.  

---

[link1]: https://www.netacad.com/courses/cybersecurity/cyberops-associate
[link2]: https://securityonionsolutions.com/
[link3]: https://bammv.github.io/sguil/index.html
[link4]: https://www.elastic.co/elastic-stack
[link5]: https://github.com/ItaloHugoMDS/Analyzing_Alerts_with_ELK_Stack#prompt
[link6]: https://github.com/ItaloHugoMDS/Analyzing_Alerts_with_ELK_Stack#questions-and-answers
[link7]: https://github.com/ItaloHugoMDS/Analyzing_Alerts_with_ELK_Stack#analysis
