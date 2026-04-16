# CASE: BOTSV1-PoisonIvy
Incident Report - PoisonIvy.

---
#1. Investigation Context

There have been 2 breaches. Identify each and write a incident report.

# 2. Executive Summary

At (8/10/16, 4:45:21.226 PM) domain iamnotbatman[.]com is compromised allowing attackers to breach server security and upload defaced banner on the website.

# 3. Victim Details

| Field       | Value             |
| ----------- | ----------------- |
| Domain      | iamnotbatman[.]com  |
| Hostname    | we1149srv  |
| IP Address  | 10.8.15.133       |
| User        | IUSR     |

# 4. Timeline

| Time(UTC)               | Events                                                                                                                    |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| 2016-08-10 16:36:45.631 | IP Address 40.80.148.42 visits iamnotbatman.com for the first time and begins vulnerability scanning via Acunetix.        |
| 2016-08-10 16:36:45.631 | IP Address 40.80.148.42 attempts injection attacks on iamnotbatman.com.                                                   |
| 2016-08-10 16:45:21.226 | IP Address 23.22.63.114 begins a brute force attack on iamnotbatman.com.                                                  |
| 2016-08-10 16:46:33.689 | IP Address 23.22.63.114 successfully gains access to admin account (weak credentials) on iamnotbatman.com.                |
| 2016-08-10 16:48:05.858 | IP Address 40.80.148.42 successfully logs in to the compromised admin account.                                            |
| 2016-08-10 16:50:31.324 | IP Address 40.80.148.42 installs a file named agent.php via Joomla extensions.                                            |
| 2016-08-10 16:52:47.035 | IP Address 40.80.148.42 uploads "3791.exe" to server we1149srv via agent.php backdoor.                                    |
| 2016-08-10 16:56:18.000 | we1149srv@192.168.250.70 executes 3791.exe, initiating network and username scanning activity.                            |
| 2016-08-10 17:06:21.569 | we1149srv@192.168.250.70 retrieves "poisonivy-is-coming-for-you-batman.jpeg" from prankglassinbracket.jumpingcra.com:1337 |
| 2016-08-10 17:20:33.000 | we1149srv@192.168.250.70 replaces original site banner image via cmd.exe, resulting in website defacement.                |


# 5. Methodology

* Performed initial log review using Splunk to identify anomalies through high-level statistics such as top source IPs, most accessed URLs, and frequent request patterns.

* Analyzed HTTP traffic in detail, including reconstruction of HTTP streams, to identify malicious requests, payload delivery, and exploitation activity.

* Reviewed authentication events and session data, including cookies and login activity, to detect unauthorized access and abnormal session behavior.

* Correlated IP address activity across multiple log sources to track attacker actions over time, including reconnaissance, exploitation, privilege escalation, and post-compromise activity.

* Examined endpoint and process execution logs to identify malicious file transfers, payload execution, and system-level compromise indicators.

* Correlated all relevant events chronologically to construct a unified attack timeline from initial reconnaissance through to final impact.

# 6. Findings

### 6.1 Initial Access

The Malicious IP 23.22.63.114 gains admin access via bruteforce for imanotbatman[.]com. Login details are then used by 40.80.148.42 to gain access and upload agent.php via Joolma extentions.


### 6.2 First stage

Shortly after upload of the backdoor, 40.80.148.42 uploads a malicious file onto the server "3791.exe".

### 6.3 Second stage

Server we1149srv executes malicious code which prompts it to download a .jpeg file, rename it, and replace the original banner jpg with the new downloaded jpeg.

# 7. IOCs

| IOC Type                         | Value                                             | Description                                                                          |
| -------------------------------- | ------------------------------------------------- | ------------------------------------------------------------------------------------ |
| Authentication Attack            | Brute force via weak credentials                  | Unauthorized access achieved through password guessing against admin account         |
| Web Backdoor                     | `agent.php`                                       | Malicious Joomla extension used as a backdoor on iamnotbatman.com                    |
| Malicious File                   | `3791.exe`                                        | Executable payload uploaded via `agent.php` to server `we1149srv` (`192.168.250.70`) |
| Host                             | `we1149srv` / `192.168.250.70`                    | Compromised internal server where payload was executed                               |
| Command & Control / Download URL | `http://prankglassinebracket.jumpingcrab.com`     | External domain used to host/download secondary payload                                                                   
| File Renaming Activity           | `poisonivy-is-coming-for-you-batman.jpeg → 2.jpg` | Payload renamed after download                                                       |
| Web Defacement                   | `2.jpg replacing original banner image`           | Original website banner replaced with malicious image, resulting in defacement       |



# 8. File Hashes

- SHA256 of 3791.exe: EC78C938D8453739CA2A370B9C275971EC46CAF6E479DE2B2D04E97CC47FA45D



# 9. Conclusion
Hackers initially gained access through a brute-force attack, which was successful due to the use of weak credentials. Following this, the adversary obtained administrative access and exploited a vulnerability in the Joomla extension framework, enabling the upload of malicious files directly to the server.

The executable 3791.exe was detected by the Fortinet FortiGate IPS (gotham-fortigate) as malicious; however, the configured security policy was set to monitoring mode rather than blocking, allowing the file transfer to proceed.

Recommendations:
* Review all authentication logs and enforce strong password policies, including complexity requirements and account lockout mechanisms.
* Update and tune IPS/antivirus policies to ensure known malicious signatures are actively blocked rather than monitored.
* Isolate and contain the affected server, followed by full malware eradication and system validation.
* Conduct a thorough review of logs; no evidence of lateral movement or privilege escalation was identified based on current analysis.
