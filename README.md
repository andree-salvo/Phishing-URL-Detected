# 🛡️ SOC141 — Phishing URL Detected

### SOC Incident Investigation | LetsDefend

![Platform](https://img.shields.io/badge/Platform-LetsDefend-00b7b0?style=flat-square) ![Severity](https://img.shields.io/badge/Severity-High-red?style=flat-square) ![Verdict](https://img.shields.io/badge/Verdict-True%20Positive-success?style=flat-square) ![Score](https://img.shields.io/badge/Playbook-10%2F10-blue?style=flat-square)

**Analyst:** Andree Salvo · **Event ID:** 86 · **Difficulty:** Beginner · **Status:** Closed

## 📚 Table of Contents

- [Executive Summary](https://github.com/andree-salvo/Phishing-URL-Detected/edit/main/README.md#-executive-summary)
- [Incident Overview](https://github.com/andree-salvo/Phishing-URL-Detected/edit/main/README.md#-incident-overview)
- [Investigation Objectives](https://github.com/andree-salvo/Phishing-URL-Detected/edit/main/README.md#-investigation-objectives)
- [Investigation Walkthrough](https://github.com/andree-salvo/Phishing-URL-Detected/edit/main/README.md#-investigation-walkthrough)
- [Indicators of Compromise](https://github.com/andree-salvo/Phishing-URL-Detected/edit/main/README.md#-indicators-of-compromise-iocs)
- [MITRE ATT&CK Mapping](https://github.com/andree-salvo/Phishing-URL-Detected/edit/main/README.md#-mitre-attck-mapping)
- [Impact Assessment](https://github.com/andree-salvo/Phishing-URL-Detected/edit/main/README.md#%EF%B8%8F-impact-assessment)
- [Recommended Remediation](https://github.com/andree-salvo/Phishing-URL-Detected/edit/main/README.md#%EF%B8%8F-recommended-remediation)
- [Final Results](https://github.com/andree-salvo/Phishing-URL-Detected/edit/main/README.md#-final-results)
- [Skills Demonstrated](https://github.com/andree-salvo/Phishing-URL-Detected/edit/main/README.md#-skills-demonstrated)
- [Conclusion](https://github.com/andree-salvo/Phishing-URL-Detected/edit/main/README.md#-conclusion)

---

## 📌 Executive Summary

I investigated a high-severity **SOC141 — Phishing URL Detected** alert in the LetsDefend simulated SOC environment. The alert identified an HTTP request from internal workstation **EmilyComp (`172.16.17.49`)** to the suspicious external domain **`mogagrocol.ru`**.

I triaged the alert, examined the suspicious URL using VirusTotal, reviewed the endpoint record, and correlated proxy and firewall telemetry. The URL was identified as malicious, and the alert recorded the HTTP request as **Allowed**. I closed the case as a **True Positive** with a **10/10 (100%) playbook score**.

> **Evidence boundary:** An allowed request to a malicious URL does not, by itself, prove credential theft, malware execution, or complete endpoint compromise.

## 📊 Incident Overview

| Field | Value |
|---|---|
| Detection | SOC141 — Phishing URL Detected |
| Event ID | `86` |
| Severity | **High** |
| Alert source | Proxy |
| Event time | `2021-03-22 21:23:12 UTC+03:00` |
| Source host | `EmilyComp` |
| Source IP | `172.16.17.49` |
| Alert username | `ellie` |
| Destination hostname | `mogagrocol.ru` |
| Destination IP (alert/log) | `91.189.114.8` |
| Destination port | `80/TCP (HTTP)` |
| Device action | **Allowed** |
| Final verdict | **True Positive** |
| Playbook score | **10/10 — 100%** |

**Evidence 01 — Alert overview**

<img width="1621" height="117" alt="1 - Alert Overview" src="https://github.com/user-attachments/assets/532aae99-f69c-406a-8fc3-135e98827961" />

*The high-severity SOC141 event appears in LetsDefend Monitoring.*

## 🎯 Investigation Objectives

- Validate whether the alert was a true positive or false positive.
- Identify the source asset, user, and destination.
- Analyze the reported URL for malicious indicators.
- Determine whether the destination was accessed using available logs.
- Document findings, limitations, and remediation recommendations.

## 🔎 Investigation Walkthrough

### Phase 1 — Initial Alert Triage

In the **Monitoring → Investigation Channel**, I reviewed Event ID `86` and recorded the source, destination, request URL, and device action. The alert attributed the activity to `ellie` on `EmilyComp` and showed **Allowed** for the device action.

**Evidence 02 — Alert triage and event details**

<img width="1918" height="829" alt="2 - Alert Triage" src="https://github.com/user-attachments/assets/00948769-ed9c-4912-86c8-dbeb3f51eeaa" />

**Analyst finding:** A high-severity detection involved an allowed HTTP request from an internal workstation to a flagged URL, warranting independent URL and log review.

### Phase 2 — Suspicious URL and Threat Intelligence Analysis

**Defanged URL:**

```text
hxxp://mogagrocol[.]ru/wp-content/plugins/akismet/fv/index.php?email=ellie@letsdefend.io
```

| URL component | Observation |
|---|---|
| Domain | `mogagrocol.ru` |
| Path | `/wp-content/plugins/akismet/fv/index.php` |
| Parameter | `email=ellie@letsdefend.io` |
| Scheme | HTTP (unencrypted) |

The path resembles a WordPress plugin location. The email parameter could facilitate recipient-specific tracking, but does not alone establish credential harvesting.

In the captured **VirusTotal URL report**, **10 of 93 security vendors** flagged the URL, including detections labeled *Phishing*, *Malicious*, and *Malware*. The report also displayed HTTP status `403` at the time of its analysis. VirusTotal showed `195.24.68.4` in its report, while the original LetsDefend alert and network logs identified the destination as `91.189.114.8`. These are observations from different sources/times and should not be treated as the same IP address.

**Evidence 03 — VirusTotal detection results**

<img width="1913" height="940" alt="3 - VirusTotal results" src="https://github.com/user-attachments/assets/e3df07fb-3de3-4c37-9501-b13dce2509d6" />

**Analyst finding:** The URL was treated as **Malicious** in the LetsDefend playbook, consistent with multiple malicious/phishing vendor classifications in the captured VirusTotal report.

### Phase 3 — Endpoint Review

I located the affected machine in **Endpoint Security** and reviewed its asset details and visible browser history.

| Endpoint field | Value |
|---|---|
| Hostname | `EmilyComp` |
| IP address | `172.16.17.49` |
| Operating system | Windows 10, 64-bit |
| Domain | LetsDefend |
| Primary user in endpoint inventory | Emily |
| Containment indicator in screenshot | **Host Contained** |

**Evidence 04 — Endpoint inventory and browser history**

<img width="1914" height="945" alt="4 - Endpoint Security" src="https://github.com/user-attachments/assets/861c9844-a21c-4559-8fab-4fbf8ac57049" />

**Analyst finding:** The endpoint inventory identified the workstation and displayed a **Host Contained** state. The screenshot does not establish when containment was applied or who performed it. The alert username (`ellie`) differs from the inventory's primary user (`Emily`); both values are retained as displayed rather than assumed to be identical.

The visible browser history entries in this screenshot predate the March 2021 alert; they should not be presented as direct proof of this particular URL visit.

### Phase 4 — Proxy and Firewall Log Analysis

I filtered **Log Management** for source IP `172.16.17.49` and reviewed network records to the alerted destination.

| Logged time (as displayed) | Source IP | Destination IP | Destination port | Log type |
|---|---|---|---|---|
| `2021-03-22 11:23:54` | `172.16.17.49` | `91.189.114.8` | `80` | Proxy |
| `2021-03-22 11:23:16` | `172.16.17.49` | `91.189.114.8` | `80` | Firewall |

The raw log popup included the same suspicious request URL. The alert itself recorded the action as **Allowed**. The difference between log-list timestamps and the alert's timestamp is preserved as displayed; the screenshots do not provide enough timezone context to reconcile them.

**Evidence 05 — Network logs and raw request URL**

<img width="1916" height="948" alt="5 - Network Log Evidence" src="https://github.com/user-attachments/assets/5be2f4a6-326d-4f46-8826-01fe516a6e38" />

**Analyst finding:** Proxy/firewall records corroborated traffic involving the source workstation and suspicious destination. In the playbook, I marked **“Has Anyone Accessed IP/URL/Domain?” → Accessed**.

### Phase 5 — Verdict and Case Closure

The observed URL reputation and network activity supported a **True Positive** determination. I submitted the playbook answers:

| Playbook question | Answer |
|---|---|
| Has Anyone Accessed IP/URL/Domain? | **Accessed** |
| Analyze URL Address | **Malicious** |

**Evidence 06 — Closed case, verdict, and 100% score**

<img width="1914" height="944" alt="6 - True Positive" src="https://github.com/user-attachments/assets/d7c7c9ec-9274-4444-aa79-c448de24b4a4" />

The closed alert displayed **True Positive**, **10/10 (100%)**, and a closure timestamp of **`2026-10-13 22:10:58 UTC`**. This is the case's closure time, not the original 2021 event time.

## 🚨 Indicators of Compromise (IOCs)

| Type | Value | Context |
|---|---|---|
| Domain | `mogagrocol.ru` | Suspicious destination |
| Alert destination IP | `91.189.114.8` | Source/destination log correlation |
| URL path | `/wp-content/plugins/akismet/fv/index.php` | Investigated path |
| Source IP | `172.16.17.49` | Internal affected asset |
| Hostname | `EmilyComp` | Internal affected asset |
| Alert username | `ellie` | Investigation context |

*Source IP, hostname, and username identify the affected environment; they are not inherently malicious IOCs.*

## 🧩 MITRE ATT&CK Mapping

| Technique | Assessment | Evidence limitation |
|---|---|---|
| [T1566 — Phishing](https://attack.mitre.org/techniques/T1566/) | Potentially relevant | URL classification supports suspected phishing, but delivery was not observed |
| [T1566.002 — Spearphishing Link](https://attack.mitre.org/techniques/T1566/002/) | **Possible, not confirmed** | No phishing message or delivery vector was shown |

## ⚠️ Impact Assessment

**Confirmed:** The internal workstation was associated with an allowed HTTP request to a URL classified as malicious. Related proxy and firewall records were observed.

**Not confirmed:** Credential submission, malicious file download, malware execution, data exfiltration, or unauthorized account access. Additional endpoint and authentication telemetry would be needed to make those determinations.

## 🛠️ Recommended Remediation

1. Block the malicious URL/domain through web filtering and appropriate network controls.
2. Review endpoint process, download, and detection telemetry for the affected workstation.
3. Search proxy, DNS, and firewall logs for other hosts communicating with the domain or destination IP.
4. Review authentication events for potential account misuse.
5. Confirm the endpoint's containment status and whether further containment is warranted.
6. Provide targeted phishing-awareness guidance and monitor for recurrence.

> These are **recommendations**, not claims that the actions were completed during the lab.

## ✅ Final Results

| Metric | Result |
|---|---|
| URL analysis | **Malicious** |
| Access verification | **Accessed** |
| Alert device action | **Allowed** |
| Final classification | **True Positive** |
| Playbook | **10/10 (100%)** |
| Case status | **Closed** |

## 🧠 Skills Demonstrated

**SOC Alert Triage** · **Phishing URL Analysis** · **VirusTotal Threat Intelligence** · **Proxy/Firewall Log Analysis** · **Endpoint Investigation** · **IOC Documentation** · **MITRE ATT&CK Mapping** · **Incident Reporting** · **Evidence-Based Classification**

## 🏁 Conclusion

This simulated investigation provided hands-on experience triaging a phishing URL alert, correlating URL reputation with network telemetry, and documenting an evidence-based **True Positive** verdict. I completed the LetsDefend playbook with a **100% score**, while maintaining a clear distinction between an observed malicious URL request and an unproven compromise.

---

**Analyst:** Andree Salvo  
**Platform:** LetsDefend  
**Scenario:** SOC141 — Phishing URL Detected  
**Purpose:** Cybersecurity training and portfolio documentation
