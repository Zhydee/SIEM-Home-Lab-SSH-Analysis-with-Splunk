# 🔐 SIEM Home Lab – SSH Analysis with Splunk

This project demonstrates how Splunk can be used to ingest and analyze SSH log files for detecting suspicious activity, failed logins, command patterns, and session behavior. The dataset was obtained from an open-source repository and represents simulated SSH interaction logs.

---
## Introduction
SSH (Secure Shell) log files contain valuable information about remote access to servers, including login attempts, commands executed, and session details. Analyzing SSH logs using Splunk SIEM enables security professionals to monitor access to critical systems, detect anomalies, and identify potential security threats.

## 📁 Data Source

- **Source file**: `ssh_logs.json`  
- **Sourcetype**: `_json`  
- **Event count**: 3000 events  
- **Format**: Structured JSON containing fields such as `ts`, `status_code`, `uid`, `id.orig_h`, `method`, `event_type`

### ⚠️ Disclaimer  
This project is based on the guidance and dataset provided in the open-source GitHub repository by [@0xrajneesh](https://github.com/0xrajneesh):  
[project#4-analyzing-ssh-logs-using-splunk-siem](https://github.com/0xrajneesh/Splunk-Projects-For-Beginners/blob/main/project%234-analyzing-ssh-logs-using-splunk-siem.md#3-analyze-ssh-activity-patterns)

Special thanks for the step-by-step breakdown and the sample SSH log data, which were instrumental in helping me learn and apply log analysis techniques using Splunk.

---

## ✅ 1. Extract Relevant Fields

![Data Uploaded to Splunk](https://private-user-images.githubusercontent.com/67587985/471042363-26721a61-486f-4a57-9316-58383d62aaa0.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTM1MDQzNzgsIm5iZiI6MTc1MzUwNDA3OCwicGF0aCI6Ii82NzU4Nzk4NS80NzEwNDIzNjMtMjY3MjFhNjEtNDg2Zi00YTU3LTkzMTYtNTgzODNkNjJhYWEwLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MjYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzI2VDA0Mjc1OFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTIxOTc5NzZkMTRmYWM2ZmVjMGUyNzY0YzIwMDJmMmI2YTBjMTczOGU4NzIyOWY1ZTcwYmIwNmI2OTFjMzRlZTQmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.RH0Siwj57tINuAKrzyWzISxX_MnkWhHfdzHHxLQgBaE)

<p align="center"><b>Figure 1:</b> Data successfully uploaded into Splunk.</p>

### ➤ What was done:
Splunk automatically extracted all important fields from the JSON structure, including:
### ➤ Useful Extracted Fields that I Found Useful are:

| Field           | Description                                                                 |
|-----------------|-----------------------------------------------------------------------------|
| `event_type`     | Log category (e.g. Standard, Client Error)                                  |
| `id.orig_h`      | Source IP                                                                   |
| `id.resp_h`      | Destination IP                                                              |
| `method`         | HTTP method (suggests possible web interaction via SSH tunnel or honeypot) |
| `status_code`    | HTTP response status                                                        |
| `resp_body_len`  | Response size in bytes                                                      |
| `ts`             | Timestamp                                                                   |
| `uid`            | Unique session ID                                                           |
| `uri`            | Accessed resource                                                           |
| `user_agent`     | Source client/browser or tool info                                          |


## 📊 2. Analyze SSH Activity Patterns

## 1. Distribution of SSH Methods (e.g., GET, POST)

![Data Uploaded to Splunk](https://private-user-images.githubusercontent.com/67587985/471043219-a57a065a-a5bd-4fb5-a1f9-bce117767804.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTM1MDUyMTIsIm5iZiI6MTc1MzUwNDkxMiwicGF0aCI6Ii82NzU4Nzk4NS80NzEwNDMyMTktYTU3YTA2NWEtYTViZC00ZmI1LWExZjktYmNlMTE3NzY3ODA0LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MjYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzI2VDA0NDE1MlomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWJlZGJhMWRkZWZkOTAyYjgzZTE3NjA2NWNlOGQ1MDE3M2JmMTdmZmQ4YWEzMWQwNjM5MjdlNTA1MjFjOTZiOWUmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.knViLlz9HHaTRE5NpCv_HiYBOrxax6DDEQjMxJJdq04)

<p align="center"><b>Figure 2:</b> Result of SSH Methods</p>

## 📊 Summary of Observed Method Counts

| Method  | Count |
|---------|-------|
| POST    | 1353  |
| GET     | 1346  |
| CONNECT | 77    |
| OPTIONS | 77    |
| DELETE  | 74    |
| PUT     | 73    |

## ✅ What I Observed and Concluded

I analyzed the `method` field to understand how external actors interacted with the system. The high volume of `GET` and `POST` requests suggests reconnaissance and possible data submission or exploitation attempts.

Additionally, methods like `PUT`, `DELETE`, and `CONNECT`, which are not commonly used in regular web traffic, point toward potential malicious behavior, such as file uploads, command tunneling, or attacker's attempts to clean traces.

These patterns align with typical behavior observed in SSH honeypots or attack simulations, where attackers attempt to exploit services exposed via SSH or tunneled HTTP services.

## 🎯 Purpose of Analyzing HTTP Methods in SSH Logs

Although the dataset is categorized as SSH logs, the presence of HTTP-like methods such as `GET`, `POST`, and `PUT` suggests that the traffic originates from a honeypot setup or an SSH tunnel that proxies web requests.

This could indicate one of the following:
- The system is capturing HTTP activity initiated **after** an SSH connection is established.
- The environment is emulating attacker behavior using tools like **Cowrie**, which often mimics both SSH and HTTP interactions.

By analyzing the `method` field, I aimed to:
- Understand what actions attackers attempted once they gained access.
- Detect signs of automated tools or scripts performing tasks like scanning, data submission, or file uploads.
- Identify suspicious or rare HTTP methods (e.g., `PUT`, `DELETE`) that are typically associated with attempts to modify or compromise a system.

## 2. Identify top users or source IP addresses accessing the SSH server

![Top Source IPs](https://private-user-images.githubusercontent.com/67587985/471044666-b8c3d92f-ab0c-43e9-b677-aa5a5ff7738b.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTM1MDYyMTgsIm5iZiI6MTc1MzUwNTkxOCwicGF0aCI6Ii82NzU4Nzk4NS80NzEwNDQ2NjYtYjhjM2Q5MmYtYWIwYy00M2U5LWI2NzctYWE1YTVmZjc3MzhiLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MjYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzI2VDA0NTgzOFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWMxNjIzNTQ2NzkzNGU1NTJiMTU5YTI0YWNjZjJlZTE4NmJjNTU2ZTk5NDYyYzMzNTBlMWQzYmNkNjM5YzY2MWUmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.IcA18rPnrQHCVjpObHFSvSCtf8MfnaxmDldZOuuDo0A)

<p align="center"><b>Figure 3:</b> Result of SSH The Top Source IPs</p>

## 📊 Result of Top 10 Source IPs

| IP Address | Count | % of Total Logs |
|------------|--------|------------------|
| 10.0.0.28  | 76     | 2.53%            |
| 10.0.0.42  | 73     | 2.43%            |
| 10.0.0.32  | 73     | 2.43%            |
| 10.0.0.31  | 72     | 2.40%            |
| 10.0.0.27  | 70     | 2.33%            |
| 10.0.0.45  | 70     | 2.33%            |
| 10.0.0.40  | 69     | 2.30%            |
| 10.0.0.14  | 67     | 2.23%            |
| 10.0.0.50  | 65     | 2.17%            |
| 10.0.0.47  | 65     | 2.17%            |

**Total logs:** 3,000 events  
**Combined contribution of top 10 IPs:** ~23% of total SSH interactions

## ✅ What I Observed and Concluded

I analyzed the top source IPs (`id.orig_h`) to determine which clients initiated the most SSH traffic. This helps identify possible brute-force attempts or scanning behavior.

The top 10 IPs each contributed roughly **2%–2.5%** of total logs, with no single IP dominating the activity. This suggests a **distributed pattern**, which is often a sign of automated or coordinated attacks such as **botnet-driven SSH brute force attempts**.

While all IPs in this dataset are internal (e.g., `10.x.x.x`), this same technique can be extended to real-world data by correlating **external IPs** with **geolocation** and **known malicious IP lists**.

## 🎯 Purpose of Analyzing Top Source IPs

In SSH log analysis — especially within a honeypot or lab setup — identifying the most active source IPs is a key step in understanding attack behavior and system exposure.

-  **Threat Detection**: Repeated requests from the same IP address may indicate brute-force attempts, scanning activity, or the presence of automated tools probing for weaknesses.
  
-  **Attack Attribution**: Knowing which IPs are most active allows you to cross-reference them with external threat intelligence sources, helping identify known malicious actors.

-  **Pattern Recognition**: Observing how certain IPs behave compared to the rest of the traffic can highlight anomalies and suspicious patterns that deserve further investigation.

-  **Blocking & Prevention**: In a real-world SOC scenario, identifying aggressive or abnormal IPs can inform decisions like adding them to firewall blocklists or initiating alerts.

## 3.  Analyzing HTTP Method Patterns in SSH Logs)

![Top Source IPs](https://private-user-images.githubusercontent.com/67587985/471045398-151f7d5a-a221-4c79-bcc3-38618d134f18.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTM1MDcwMjMsIm5iZiI6MTc1MzUwNjcyMywicGF0aCI6Ii82NzU4Nzk4NS80NzEwNDUzOTgtMTUxZjdkNWEtYTIyMS00Yzc5LWJjYzMtMzg2MThkMTM0ZjE4LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MjYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzI2VDA1MTIwM1omWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTFlOGViMDQ5NTY4MWEzZjRmZDBlMTllNzlmMzM4ZDcyMWQ1YmY4MGQ3NTgyYmJjMDA1MDljZmUzY2UxZmE4YTUmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.Y5mPYsO34Wa1hvsnFl7_humzswbQSzUTdu8w2MxQivg)

<p align="center"><b>Figure 4:</b> Result of HTTP Method Patterns</p>

## 📊 Result of HTTP Status Codes

| Status Code | Count |
|-------------|--------|
| 200         | 2256   |
| 404         | 400    |
| 400         | 344    |
| 500         | 30     |
| 503         | 20     |

## ✅ What I Observed and Concluded

I analyzed the distribution of HTTP status codes in the SSH log data to gain insight into attacker behavior and potential system responses. The vast majority (2,256) of the requests returned a `200 OK` status, which is expected in honeypot environments designed to simulate successful interactions.

However, significant counts of `404 Not Found` (238), `400 Bad Request` (221), `503 Service Unavailable` (145), and `500 Internal Server Error` (140) indicate a combination of active reconnaissance, scanning, and possible exploit attempts. These errors are strong indicators of automated tool use or malformed payloads.

Together, these status codes reveal an aggressive attack pattern aimed at discovering and exploiting potential vulnerabilities on the system.


## 🎯 Purpose of Analyzing All `status_code` Values in SSH Logs

Analyzing all `status_code` values helps uncover how external actors are interacting with the system beyond just success or failure. Each status code provides specific insights into attacker behavior and system responses, especially in honeypot or SSH-proxied environments.

-  **200 OK**: Indicates successful requests — attackers may have reached valid endpoints or triggered honeypot responses simulating success.

-  **400 Bad Request**: Suggests malformed input — often a sign of faulty automated tools, malformed payloads, or aggressive scanning behavior.

-  **404 Not Found**: Common in reconnaissance — attackers are probing for non-existent paths, directories, or endpoints using fuzzing or brute-force tools.

-  **500 Internal Server Error**: Indicates something failed server-side — could be due to exploitation attempts, invalid input causing backend crashes, or misconfigurations.

-  **503 Service Unavailable**: May point to system overload or intentional throttling — could reflect denial-of-service scans, rate-limit testing, or system strain.

By reviewing these codes in detail, I can better assess attacker techniques, tool behavior, and potential weaknesses exposed during the interaction.

## 📊 3. Detect Anomalies
## 📌 Objective

The goal is to detect unusual behavior in SSH activity by focusing on the following indicators:

- Time-based event spikes  
- Failed login patterns  
- Suspicious IP behavior

## 1. Look for unusual patterns in SSH activity (e.g., sudden spikes in login attempts)

![Top Source IPs](https://private-user-images.githubusercontent.com/67587985/471067109-dff7a77a-dbfb-48a9-a672-7e926a4abea7.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTM1MjY1NDMsIm5iZiI6MTc1MzUyNjI0MywicGF0aCI6Ii82NzU4Nzk4NS80NzEwNjcxMDktZGZmN2E3N2EtZGJmYi00OGE5LWE2NzItN2U5MjZhNGFiZWE3LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MjYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzI2VDEwMzcyM1omWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTJiNzlmOGEyZTVjNTBmMDcxMDNjMzJlYjg3NzVlNDQzYzJiYWMyNGJjMDc1NDJhNDVhNzQ1MzkyMGVlMjkzZjcmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.RDBuI-e9iCilqdjvMQb6XlC3FpVTXrtJ2lB5fLLpd0Y)

<p align="center"><b>Figure 5:</b> Timechart output showing the count of SSH log events over a one-hour span.</p>

## 📊 Result of timechart

| Time | Count |
|-------------|--------|
| 2025-04-25 18:00       | 3000   |

## 📉 Observation
All SSH events in this dataset occurred within a single hour, producing a single visible bar on the timechart. This suggests the dataset is time-compressed or simulated.


## 🧪 Conclusion
Even with a limited time range, this method demonstrates how time-based visualizations can help detect:

- Abnormal spikes in SSH traffic

- Potential brute-force patterns

- Unusual login activity frequency

## 2. Analyze failed login attempts

![Top Source IPs](https://private-user-images.githubusercontent.com/67587985/471067675-976737f1-c302-4524-a0e2-a11475f8553c.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTM1MjcxMzIsIm5iZiI6MTc1MzUyNjgzMiwicGF0aCI6Ii82NzU4Nzk4NS80NzEwNjc2NzUtOTc2NzM3ZjEtYzMwMi00NTI0LWEwZTItYTExNDc1Zjg1NTNjLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MjYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzI2VDEwNDcxMlomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWRlOWQ1Yjk4ZGZlODE1NjgyMjA0Y2E2YzIyNjQ0MWVmYzRjMmNjZTRhZTJhZjM2MjkxOWRiN2E4OWFjOWI3NjYmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.4NchKiW6x9Yt_OeHlXZOVrrQCe3HXrVtNOCroQu_-wE)

<p align="center"><b>Figure 6:</b> Result of failed SSH login attempts</p>

## 📊 Result of HTTP Status Codes

| Time | failed_logins |
|-------------|--------|
| 2025-04-25 18:00       | 744   |

## 📉 What I Observed

All 744 failed login attempts were logged within a single hour window (`2025-04-25 18:00`).  
This was visualized as a single bar on the Splunk timechart.

## 🧠 Why This Still Matters

Even though the dataset is time-compressed and doesn't span multiple hours or days, this approach still demonstrates:

- The value of time-based visualizations  
- How sudden spikes can reveal security threats  
- That real-world SSH logs would benefit from this technique

If this were production data, seeing hundreds of failed logins in a short time window would be a strong indicator of a brute-force attack or bot activity.

## 🧪 Conclusion

Using the condition `status_code >= 400`, I isolated failed login attempts and visualized their frequency over time.  
Although the dataset only covers a one-hour period (744 failed events), this time-based view is valuable in real-world scenarios to detect spikes in login failures.

A high number of failures in a short span is often associated with brute-force password guessing, scripted reconnaissance, or malformed request testing.

## 3. Investigate SSH sessions from unusual or suspicious source IP addresses
![Top Source IPs](https://private-user-images.githubusercontent.com/67587985/471068751-97cec61b-0830-4f31-b21b-983fc8c07374.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTM1Mjc5MzcsIm5iZiI6MTc1MzUyNzYzNywicGF0aCI6Ii82NzU4Nzk4NS80NzEwNjg3NTEtOTdjZWM2MWItMDgzMC00ZjMxLWIyMWItOTgzZmM4YzA3Mzc0LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MjYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzI2VDExMDAzN1omWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTM5MWZmNWIxMmY3NTZjOTZlYzBhYmNhMGU5MjYxYzI2ZmMzNzFiZDQxMGQ0MTBhZGE5MzU0YzczMTRiM2NmMTMmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.zXU0RpeS8rQAeTh6ezw5Ni0E4SUhmt0NPVP5SEzGgXo)

<p align="center"><b>Figure 7:</b> Event type for 10.0.0.28</p>

![Top Source IPs](https://private-user-images.githubusercontent.com/67587985/471068774-d7c3d06f-c51a-4661-8053-15515f705715.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTM1Mjc5NTAsIm5iZiI6MTc1MzUyNzY1MCwicGF0aCI6Ii82NzU4Nzk4NS80NzEwNjg3NzQtZDdjM2QwNmYtYzUxYS00NjYxLTgwNTMtMTU1MTVmNzA1NzE1LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MjYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzI2VDExMDA1MFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTE3ZDBjMzllZTFkNmNmNjU5MDBmMjlmMTMyNDU5Yzc3YjJkMDEwNDg4Yzk4ZGNiZTFlNmI3OTFiN2VkMzMxMTAmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.Oc9WCHL2L-x6qPjqDDVU0MBY8Z0iTU0L0hQ9FQHdNXU)

<p align="center"><b>Figure 8:</b> Method for 10.0.0.28</p>

![Top Source IPs](https://private-user-images.githubusercontent.com/67587985/471069143-ec5ce1c6-38c9-4187-9ca2-6b125e168dd5.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTM1MjgxMTgsIm5iZiI6MTc1MzUyNzgxOCwicGF0aCI6Ii82NzU4Nzk4NS80NzEwNjkxNDMtZWM1Y2UxYzYtMzhjOS00MTg3LTljYTItNmIxMjVlMTY4ZGQ1LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MjYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzI2VDExMDMzOFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWJjNzI3YWM4MmIyYTIzNGViMjBiZmM4OWM1OGJjMjIzODkzMzM1ODA1NDgyODMxZTczM2MwNjgzMmNhZDk1NWEmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.iegHbXEQXMtqBNiGWMy7DEVGPNuU5HQVou3izccybh8)

<p align="center"><b>Figure 9:</b> Event type for 10.0.0.42</p>

![Top Source IPs](https://private-user-images.githubusercontent.com/67587985/471069157-cc44312c-5e7b-4c49-a5e8-409df8c93291.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTM1MjgxMjUsIm5iZiI6MTc1MzUyNzgyNSwicGF0aCI6Ii82NzU4Nzk4NS80NzEwNjkxNTctY2M0NDMxMmMtNWU3Yi00YzQ5LWE1ZTgtNDA5ZGY4YzkzMjkxLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MjYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzI2VDExMDM0NVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTEwZDcxYzFhYWE3OTQxYjQwYjE4MDQxMzlkOWJjZGRjMGNlNjUwNzU3Njg4MmI0MTk5YzhiZjg5ZjU4ZDAxMTAmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.E2cUZwYNfie3hOOJlDK-KVy8ie1poBg42-5p4_xykPg)

<p align="center"><b>Figure 10:</b> Method for 10.0.0.42</p>

## 📊 Per-IP Activity Analysis

This section dives into two of the top source IP addresses based on SSH activity volume, highlighting their behavior across key fields.

---

### 🔸 IP: `10.0.0.28`

| **Field**     | **Top Values**                                                                                   | **Insights**                                                |
|---------------|--------------------------------------------------------------------------------------------------|--------------------------------------------------------------|
| `event_type`  | Standard (37%), Client Error (21%), Server Error (13%), Suspicious Agent(9.2%),                  | Shows both normal and suspicious interactions                |
| `method`      | POST (50%), GET (43%), PUT/DELETE (minor)                                                        | High POST = possible payload uploads or login attempts       |

---

### 🔸 IP: `10.0.0.42`

| **Field**     | **Top Values**                                                                                   | **Insights**                                                |
|---------------|--------------------------------------------------------------------------------------------------|--------------------------------------------------------------|
| `event_type`  | Standard (41%), Client Error(19%), Suspicious Agent(10%)                                         | Server Errors and suspicious patterns suggest aggressive or invalid use |
| `method`      | GET (52%), POST (42%), DELETE/PUT (minor)                                                        | GET-dominant = reconnaissance or probing                     |

## ✅ What I Observed and Concluded

I analyzed the behavior of the top two source IPs (`10.0.0.28` and `10.0.0.42`) to determine the nature of their interactions with the system.

The IP `10.0.0.28` demonstrated behavior consistent with **exploitation or payload delivery attempts**, showing a high percentage of **POST** requests along with several **PUT** and **DELETE** methods. Additionally, the presence of event types like **"Suspicious Agent"** and **"Suspicious URI"** supports the hypothesis of malicious activity intended to compromise the system.

In contrast, IP `10.0.0.42` exhibited a pattern dominated by **GET** requests and a notable number of **Server Error** and **Unexpected Method** responses. This aligns more with **reconnaissance or probing behavior**, possibly using automated tools to scan for exploitable paths or services.

These observed patterns are valuable in real-world SOC environments to distinguish **active exploit attempts** from **reconnaissance bots**, allowing teams to **prioritize response** and improve detection strategies.

## 🎯 Purpose of Investigating Top Source IPs in SSH Logs

Investigating the top source IPs is a critical part of SSH log analysis, as it provides insight into how different actors interact with the system and reveals patterns that may indicate malicious behavior.

By analyzing the top IPs, I aimed to understand the behavior profile of each source — whether they exhibited signs of aggressive scanning, exploitation attempts, or more passive interaction. Certain combinations of `event_type` and `method` fields help identify these patterns more clearly, especially when linked to failed login attempts or unusual method usage.

This analysis also supports attacker classification. For example, repeated requests using common brute-force signatures can point to automated tools, while more targeted or varied activity may suggest more advanced or hands-on attackers.

Overall, source IP analysis helps distinguish between random noise, automated recon, and deliberate exploitation, which is vital for effective threat detection and response.

## 📊 4. Monitor User Behavior
## 1. Failed Attempts by Session ID (uid)

![Top Source IPs](https://private-user-images.githubusercontent.com/67587985/471074714-41b589f9-848a-4f23-af02-098c4baf0da1.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTM1MzA5NjksIm5iZiI6MTc1MzUzMDY2OSwicGF0aCI6Ii82NzU4Nzk4NS80NzEwNzQ3MTQtNDFiNTg5ZjktODQ4YS00ZjIzLWFmMDItMDk4YzRiYWYwZGExLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MjYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzI2VDExNTEwOVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTBiM2I4YWJmNGI1MThjYTEyYmExNjhlOTZlNjViNDJkZjMxNTI5Y2UwYzhlYTlmMDM4OTJkYmY5M2RlOTEzOGImWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.9sHRQqeUWGi_f9mUEe62_fJGBcZrd4FlNcln-dLz8as)

<p align="center"><b>Figure 11:</b> Result of identifying failed login attempts by session</p>

## 📊 Result of HTTP Status Codes

| Seesion uid | failed_attempts |
|-------------|--------|
| HT1002482   | 1      |
| HT1010816   | 1      |
| HT1021105   | 1      |
....
Based on the data, I noticed that each failed login attempt was linked to a different session ID (`uid`), meaning every session only had one failure.

There were a total of **744 failed login events**, and all of them came from **744 unique sessions**. This pattern suggests the use of automated tools that rotate session IDs to avoid detection or rate-limiting, which is something attackers often do to stay under the radar.

## ✅ What I Observed and Concluded

To monitor user behavior, I analyzed **failed login attempts using the `uid` (session ID)** field, since the dataset didn’t contain any actual user account data.

The results showed that all **744 failed login attempts** were each tied to a **unique session ID**, with no repetition. This strongly suggests the use of **automated attack scripts** that intentionally generate a **new session for every attempt** — a known tactic to evade **rate-limiting**, **brute-force protections**, and detection by tools that rely on repeated attempts from the same source.

Even though this behavior doesn’t show repeated failures within a single session, it clearly points to a **distributed attack pattern**, which is much harder to detect unless advanced **cross-session correlation** or **source IP tracking** is in place.

This type of pattern is critical for SOC teams to understand, as it demonstrates how attackers adapt to basic detection thresholds by rotating identifiers across their attempts.

## 🎯 Purpose of Analyzing Failed Logins by `uid` (Session ID)

Even though the dataset lacks a `user` field, analyzing failed login attempts based on the `uid` (session ID) still provides meaningful insights into attacker behavior at the session level.

By focusing on `uid`, I was able to detect sessions with repeated failed login attempts, which is a strong indicator of brute-force activity or credential spraying tools. When a single session repeatedly fails to authenticate, it often points to automated tools attempting password guesses across multiple accounts.

This method also gives SOC analysts session-level visibility. Even without knowing the actual username, tracking activity by session reveals how attackers move through the system, how persistent they are, and what methods they use.

Additionally, analyzing by `uid` helps reduce noise in the logs by narrowing focus to particularly “loud” sessions — those that generate large volumes of failed attempts. This makes it easier to trigger alerts or apply thresholds, enabling faster response to likely threats.

## 2. Analyze Session Duration

![Top Source IPs](https://private-user-images.githubusercontent.com/67587985/471076849-3c35d28a-dfde-4989-8cf2-fcb81c5dc5ab.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTM1MzIwNDEsIm5iZiI6MTc1MzUzMTc0MSwicGF0aCI6Ii82NzU4Nzk4NS80NzEwNzY4NDktM2MzNWQyOGEtZGZkZS00OTg5LThjZjItZmNiODFjNWRjNWFiLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MjYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzI2VDEyMDkwMVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTRkZDk1ZjcwMjc2NTRlZmIyZGM1YWFhY2MzMWJjODBiOGU1NjZiZmFiYjI0ZDlmYzU1NWMzMzM0YzA2OWQ4YjEmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.5Ko69zDL1r0aOViafXoVYbBynSbvfPOe5kq1m-wfnPg)

<p align="center"><b>Figure 11:</b> Result of identifying failed login attempts by session</p>

## 📊 Result of HTTP Status Codes

| Seesion uid | failed_duration |
|-------------|--------|
| HT1002482   | 0.00000      |
| HT1010816   | 0.00000      |
| HT1021105   | 0.00000      |
....
## 🧠 Why Session Durations Are 0

While attempting to calculate session durations using the `ts` (timestamp) field and `uid` (session ID), I found that each session only had a single log entry or all entries shared the exact same timestamp. As a result, the calculation of `max(ts) - min(ts)` per session always resulted in zero.

This is likely because the dataset is simulated — it comes from a public repository and seems to be designed primarily for basic detection or pattern analysis rather than modeling full session behavior. The absence of multiple time-staggered events per session means duration-based analysis isn’t meaningful in this case.

## ✅ What I Observed and Concluded

I attempted to analyze session durations by calculating the time difference between the first and last event (`ts`) for each session (`uid`). However, the results showed that every session had either a single timestamp or multiple events with identical timestamps. This caused all calculated durations to be `0`.

This observation suggests that the dataset logs only one key event per session, rather than tracking full session lifecycles. It's likely a limitation of the simulated dataset — which appears to be designed for basic detection and pattern recognition rather than detailed session-based analysis. As such, session duration insights were not possible in this scenario.


## 🧾 5. Splunk Report

This section showcases my ability to create and export reports directly from Splunk based on SSH log data analysis. These reports highlight key metrics such as request methods, status codes, and top source IPs—even when using synthetic or simulated datasets.

### 📝 Report Title: `SSH Activity Summary Report`
- **Export Format**: `.pdf`
- **Purpose**: Demonstrate proficiency in transforming search results into structured, shareable executive reports using Splunk's reporting functionality.
- **Note**: These reports focus on presenting clear metrics visually rather than providing exhaustive analysis.

### 📄 Reports Included
| Report Title | Description |
|--------------|-------------|
| [SSH_Request_Methods_Distribution-2025-07-26.pdf](SSH_Request_Methods_Distribution-2025-07-26.pdf) | Summarizes the frequency of different HTTP/SSH methods (GET, POST, PUT, DELETE, etc.) used in the log data. |
| [HTTP_Status_Code_Breakdown_in_SSH_Logs-2025-07-26.pdf](HTTP_Status_Code_Breakdown_in_SSH_Logs-2025-07-26.pdf) | Displays the count and distribution of HTTP status codes (e.g., 200, 404, 503), useful for spotting failed or malicious interactions. |
| [SSH_Top_Source_IPs-2025-07-26.pdf](SSH_Top_Source_IPs-2025-07-26.pdf) | Lists the most frequent IP addresses initiating SSH sessions or requests, helping to identify potential attackers or scanning sources. |

📌 _These reports serve as evidence of my technical skill in creating and formatting Splunk reports, suitable for team briefings, stakeholder updates, or audits._


## 🧩 6. Splunk Dashboard

### 🎯 Dashboard Title: `SSH Log Behavior Overview`

**Panels included**:
- **Top 10 Source IPs**: Horizontal bar chart showing top IPs by activity  
- **HTTP Status Code Breakdown**: Pie chart showing 200, 404, 400, 500, etc.  
- **Request Method Distribution**: Pie chart showing GET, POST, PUT, DELETE  

🔍 **Description**:  
> This dashboard provides a visual overview of SSH log behavior, including the most active source IPs (potential attackers), the types of HTTP request methods observed, and the distribution of HTTP response status codes. It helps analysts quickly identify unusual patterns in SSH traffic.

📄 **Dashboard PDF**:  
- [ssh_log_behavior_overview-2025-07-26.pdf](ssh_log_behavior_overview-2025-07-26.pdf)


## 🧠 Conclusion

This project demonstrated key Splunk capabilities:
- Field extraction and search optimization  
- Anomaly detection using time-based visualizations  
- Attack surface profiling based on source IPs  
- Behavioral pattern analysis using session metadata  
- Creation of interactive dashboards and exportable reports  

Although the dataset was simulated and time-constrained, the methods and skills applied reflect real-world SIEM use cases and blue-team analysis workflows.

💡 **Key takeaways from this home lab**:
- Gained experience in identifying critical patterns in SSH logs  
- Learned what to analyze to understand potential attacker behavior  
- Practiced extracting valuable insights from SSH logs using Splunk’s search language and visualization tools
