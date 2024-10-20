- [Project](#project)
  - [Lab Overview](#lab-overview)
    - [Lab](#lab)
  - [Setup](#setup)
  - [Preparation](#preparation)
  - [Identification](#identification)
    - [Alert](#alert)
    - [Suspicious Website](#suspicious-website)
    - [Download Confirmed](#download-confirmed)
  - [Containment](#containment)
    - [Workstation Isolated](#workstation-isolated)
  - [Eradication](#eradication)
    - [Remove-Rescan](#remove-rescan)
  - [Recovery](#recovery)
    - [Backup-Solution](#backup-solution)
  - [Lessons Learned](#lessons-learned)
    - [Document](#document)
    - [Exercise Summary](#exercise-summary)


# Project
Exercise for using the PICERL Incident Handling process for handling a simulated spear phishing incident [T1566.001](https://attack.mitre.org/techniques/T1566/001/). 

## Lab Overview
Microsoft Advanced Threat Analytics is set up on the WEF machine, and a lightweight ATA gateway is on the DC.  There are pre-installed tools like Splunk, Powershell logging, and Sysmon to help monitor and log system activity.  Additionally, Zeek and Suricata are configured to track network traffic, while Guacamole allows users to access all hosts from their web browser.

### Lab
<img alt="Lab" src="/Images/lab.png" width=50% height=50%>

## Setup
The lab used is the [Detection Lab](https://github.com/clong/DetectionLab)﻿ deployed locally via VMware Workstation Pro. The Vagrant-installed lab is no longer being maintained as of January 1, 2023. Found a recent not-too-broken [fork](https://github.com/Hwave/DetectionLabBlueTeam)  to install and troubleshooted any other issues I came across. The main issue was a syntax error for velociraptor install, causing it to stop the Vagrant install. This [commit](https://github.com/clong/DetectionLab/commit/4aa5abca49ab67df9f2f5c02b4741309cf22f6a7#diff-c079b88a60335adf5a556b8f957472b3d71a16fcd8cb767c770b1f0e6b8d3af5L449) fixed the syntax issue but also a Guacamole SSL issue, which was nice to have, but Guacamole was not necessary since the lab was installed locally.





## Preparation
Getting to know the steps involved in responding to security incidents is really important. This means learning how to handle situations when something goes wrong. It also involves making sure they can access the tools and systems needed to do their job effectively. Having the right resources is crucial for resolving issues quickly. By understanding these protocols and ensuring they have access, they can help keep the organization safe from cyber threats.



## Identification
A daily scheduled alert on Splunk returned a macro excel file on host 'win10'. The alert works by searching EventCode=11 and filtering for only macro office documents. Since the source is not yet known or if the file is malicious, an investigation was started. For this simulation, the file is downloaded via AtomicRedTeam. In reality, this macro document may have been stopped by an Email Security Gateway, but there are cases where users are allowed to use macros. The first move is to find the source of the downloaded file using Splunk. While searching the Zeek dns.log file in the SIEM for unique DNS queries that occurred within 5 seconds before or after the FileCreate event, a query for the github.com domain was found. The domain is considered suspicious in this simulation. AbuseIPDB would be a good resource to find if a website is suspicious. Only knowing that the website was DNS queried is not enough to prove the website was the source of the '.xlsm' file. Next searching for only 'xlsm' and 'github' returns the web request for the file download, proving the file was downloaded from the suspicious website. Finally scanning the file in VirusTotal returned positive scans.

### Alert
<img alt="Alert1" src="/Images/alert1.png" width=50% height=50%>

### Suspicious Website
<img alt="Alert2" src="/Images/alert2.png" width=50% height=50%>

### Download Confirmed
<img alt="Alert3" src="/Images/alert2.png" width=50% height=50%>


## Containment
The endpoint win10.windomain.local would need to be isolated, IPs and domains would need to be blacklisted in the network including for emails. The phishing emails would need to be removed. 

### Workstation Isolated
<img alt="Isolate" src="/Images/isolate.png" width=50% height=50%>


## Eradication
After removing the document. Using Velociraptor a threat hunt was used that scanned all windows endpoints for the all macro documents extensions like 'xlsm'. No results were found.
### Remove-Rescan
<img alt="Remove-Rescan" src="/Images/remove-rescan.png" width=50% height=50%>

## Recovery
During the recovery phase a backup solution would be used. This may be a re-imaging of the workstation but in this case a snapshot restore would be the backup solution.
### Backup-Solution
<img alt="Backup-Solution" src="/Images/backup-solution.png" width=50% height=50%>

## Lessons Learned
### Document
At this point the incident would be resolved and the process can be documented using the internal document/ticket process. Also, any changes can be recommended to prevent reoccurring incidents.
### Exercise Summary  
This project provided valuable insights into effectively handling spear phishing incidents using the PICERL Incident Handling process. One key takeaway is the importance of utilizing monitoring tools like Splunk to detect suspicious activities, such as the download of macro files from potentially harmful websites. Additionally, we learned that a swift response isolating the affected computer and removing the malicious file is crucial for minimizing risk. Finally, documenting the incident thoroughly helps us refine our security measures and prepares us for better responses in the future.