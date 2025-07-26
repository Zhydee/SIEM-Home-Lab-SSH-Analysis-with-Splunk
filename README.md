# 🔐 SIEM Home Lab – SSH Analysis with Splunk

This project demonstrates how Splunk can be used to ingest and analyze SSH log files for detecting suspicious activity, failed logins, command patterns, and session behavior. The dataset was obtained from an open-source repository and represents simulated SSH interaction logs.

---

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

## 2. Identify top users or source IP addresses accessing the SSH server:

![Top Source IPs](https://private-user-images.githubusercontent.com/67587985/471044666-b8c3d92f-ab0c-43e9-b677-aa5a5ff7738b.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTM1MDYyMTgsIm5iZiI6MTc1MzUwNTkxOCwicGF0aCI6Ii82NzU4Nzk4NS80NzEwNDQ2NjYtYjhjM2Q5MmYtYWIwYy00M2U5LWI2NzctYWE1YTVmZjc3MzhiLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MjYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzI2VDA0NTgzOFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWMxNjIzNTQ2NzkzNGU1NTJiMTU5YTI0YWNjZjJlZTE4NmJjNTU2ZTk5NDYyYzMzNTBlMWQzYmNkNjM5YzY2MWUmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.IcA18rPnrQHCVjpObHFSvSCtf8MfnaxmDldZOuuDo0A)

<p align="center"><b>Figure 2:</b> Result of SSH The Top Source IPs</p>

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

<p align="center"><b>Figure 2:</b> Result of HTTP Method Patterns</p>

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


