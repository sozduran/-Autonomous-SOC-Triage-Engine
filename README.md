# 🛡️ Autonomous SOC Triage Engine: Wazuh SIEM + Groq LPU Integration

An autonomous, ultra-low-latency SOC Triage and Threat Analysis automation built for **Wazuh SIEM**. This integration bridges enterprise SIEM detection with **Groq LPU (Language Processing Unit)** hardware acceleration to analyze high-severity alerts in sub-second speed.

![Wazuh](https://img.shields.io/badge/Wazuh-4.9.0-blue?style=for-the-badge&logo=wazuh)
![Groq](https://img.shields.io/badge/Groq-LPU_Accelerated-orange?style=for-the-badge)
![Python](https://img.shields.io/badge/Python-3.x-green?style=for-the-badge&logo=python)
![Latency](https://img.shields.io/badge/Avg_Latency-~250ms-brightgreen?style=for-the-badge)

---

## 📌 Executive Summary & Core Engineering Breakthrough

In modern Security Operations Centers (SOC), alert fatigue and high-volume Level 7+ events require rapid automated triage. Traditional approaches using local LLM models (e.g., Ollama running on local CPU/GPU) introduce significant bottlenecks:

* **The Problem:** Local CPU-bound LLMs take **30 to 120 seconds** per alert, causing `wazuh-integratord` timeouts, process queue backups, and server resource exhaustion.
* **The Solution:** Offloading contextual triage to Groq's custom LPU architecture via an event-driven Python integration script.
* **The Result:** Reduced triage response time from **~60 seconds to ~250 milliseconds** while bypassing WAF restrictions through custom headers and payload sanitization.

---

## 🏗️ System Architecture & Data Flow

```text
+--------------------------+
|  Linux Server Logs       |
| (/var/log/auth.log)      |
+------------+-------------+
             | (1. SSH Brute Force / Anomaly Detection)
             v
+--------------------------+
|  Wazuh Manager           |
|  (wazuh-analysisd)       |  <-- Evaluates Rules & Severity Levels
+------------+-------------+
             | (2. Triggers Rule >= Level 7)
             v
+--------------------------+
|  Wazuh Integrator        |
|  (wazuh-integratord)     |  <-- Reads ossec.conf Integration Block
+------------+-------------+
             | (3. Passes JSON Alert to Integration Script)
             v
+-------------------------------------------------+
|  custom-ollama-triage.py                        |
|  - Cloudflare WAF Bypass (Custom User-Agent)    |
|  - JSON Payload Sanitization                    |
+------------+------------------------------------+
             | (4. HTTPS API Request)
             v
+--------------------------+
|  Groq LPU Cloud API      |  <-- Sub-second Inference (openai/gpt-oss-20b)
+------------+-------------+
             | (5. Structured Triage Assessment)
             v
+--------------------------+
|  /var/ossec/logs/        |
|  integrator.log          |  <-- Logged for SOC Analyst Review / Active Response
+--------------------------+
