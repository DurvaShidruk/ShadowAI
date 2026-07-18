<div align="center">

# 🛡️  ShadowAI

### AI Prompt Security & Governance Platform

**Stops sensitive data leaking into ChatGPT, Claude, Gemini, Copilot, and Perplexity — scanned in the browser, before it ever sends.**

<br/>

![Status](https://img.shields.io/badge/status-active-22c55e?style=for-the-badge&logo=statuspage&logoColor=white)
![FastAPI](https://img.shields.io/badge/backend-FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![Python](https://img.shields.io/badge/python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)


---

</div>

<br/>

## Why this exists

People paste things into AI chatbots without thinking twice as an AWS key while debugging, a customer's email while drafting a support reply, a chunk of proprietary code while asking for a refactor. None of it is malicious. All of it is a problem.

**ShadowAI** sits between the employee and the AI tool, reads the prompt in real time, and decides whether it's safe to send entirely inside the browser, before a single character reaches an external server.

No blind trust. No silent blocking either every decision comes with a reason attached.

<br/>

## How it flows

```mermaid
flowchart TD
    A["Employee"] --> B["Browser Extension"]
    B -->|intercepts before send| C["FastAPI Backend"]
    C --> D["Detection Engine"]
    D --> E["Risk Scoring Engine"]
    E --> F{"Decision Engine"}
    F -->|0-30| G["Allow"]
    F -->|31-70| H["Warn"]
    F -->|71-100| I["Block"]
    F -->|when possible| J["Auto-Redact"]
    G --> K["Audit Logs"]
    H --> K
    I --> K
    J --> K
    K --> L["Admin Dashboard"]

    style A fill:#1E293B,stroke:#64748B,color:#fff
    style B fill:#0F172A,stroke:#3B82F6,color:#fff
    style C fill:#0F172A,stroke:#3B82F6,color:#fff
    style D fill:#0F172A,stroke:#8B5CF6,color:#fff
    style E fill:#0F172A,stroke:#8B5CF6,color:#fff
    style F fill:#1E293B,stroke:#F59E0B,color:#fff
    style G fill:#0F172A,stroke:#22C55E,color:#fff
    style H fill:#0F172A,stroke:#F59E0B,color:#fff
    style I fill:#0F172A,stroke:#EF4444,color:#fff
    style J fill:#0F172A,stroke:#06B6D4,color:#fff
    style K fill:#1E293B,stroke:#64748B,color:#fff
    style L fill:#1E293B,stroke:#64748B,color:#fff
```

The whole round trip — intercept, scan, score, decide — happens before the prompt is ever allowed to leave the machine.

<br/>

## What's actually checking the prompt

ShadowAI doesn't rely on one model doing all the work. Three detection modules run in parallel, each tuned for a different kind of leak.

```mermaid
flowchart LR
    P["Incoming Prompt"] --> S["Secret Detection"]
    P --> PII["PII Detection"]
    P --> SC["Source Code Detection"]

    S --> R["Explainable Risk Score"]
    PII --> R
    SC --> R

    style P fill:#1E293B,stroke:#64748B,color:#fff
    style S fill:#0F172A,stroke:#EF4444,color:#fff
    style PII fill:#0F172A,stroke:#3B82F6,color:#fff
    style SC fill:#0F172A,stroke:#22C55E,color:#fff
    style R fill:#1E293B,stroke:#F59E0B,color:#fff
```

<div align="center">

| Module | Catches | Powered by |
|:---|:---|:---|
| ![Secrets](https://img.shields.io/badge/-Secret_Detection-EF4444?style=flat-square&logo=letsencrypt&logoColor=white) | AWS keys, GitHub tokens, JWTs, passwords, API keys, OAuth tokens, SSH keys | Regex + pattern matching |
| ![PII](https://img.shields.io/badge/-PII_Detection-3B82F6?style=flat-square&logo=googlecloud&logoColor=white) | Emails, phone numbers, credit cards, Aadhaar, PAN, passport numbers, customer names | Microsoft Presidio, spaCy NER, regex |
| ![Code](https://img.shields.io/badge/-Source_Code_Detection-22C55E?style=flat-square&logo=codeium&logoColor=white) | Python, Java, JavaScript, C++, Go, SQL | Keyword, import, class/function, and file-structure heuristics |

</div>

Running these in parallel instead of stacking them keeps accuracy high without drowning users in false positives.

<br/>

## Risk scoring, with receipts

Most tools in this space hand you a number and call it a day. ShadowAI shows its work.

```
Risk Score: 87

  AWS Access Key       +40
  Customer Email       +20
  Source Code          +15
  Public AI Platform   +12
```

Every risk score is fully explainable, with each point directly linked to the specific sensitive content or behavior detected by the engine—ensuring complete transparency, not blind trust.

<div align="center">

| Score | Action | Behavior |
|:---:|:---|:---|
| `0–30` | ![Allow](https://img.shields.io/badge/Allow-22C55E?style=flat-square) | Prompt goes through, no friction |
| `31–70` | ![Warn](https://img.shields.io/badge/Warn-F59E0B?style=flat-square) | User sees the risk and chooses: cancel, send anyway, or auto-redact — either way, it's logged |
| `71–100` | ![Block](https://img.shields.io/badge/Block-EF4444?style=flat-square) | Prompt is stopped per policy, with a clear explanation shown on screen |

</div>

<br/>

## Auto-redaction, not just blocking

Blocking someone outright kills their momentum. Where it's safe to do so, ShadowAI just strips the sensitive part and lets the person keep working.

<table>
<tr>
<td width="50%" valign="top">

**Before**
```
AWS_KEY=AKIA123456789
Customer: john@example.com
```

</td>
<td width="50%" valign="top">

**After**
```
AWS_KEY=[REDACTED]
Customer: [EMAIL]
```

</td>
</tr>
</table>

<br/>

## Full data flow

```mermaid
flowchart TD
    A["Employee"] --> B["Browser Extension"]
    B --> C["FastAPI Backend"]
    C --> D["Detection Engine"]

    subgraph DET[" "]
        D1["Secret Detection"]
        D2["PII Detection"]
        D3["Source Code Detection"]
    end

    D --> D1
    D --> D2
    D --> D3

    D1 --> E["Explainable Risk Scoring"]
    D2 --> E
    D3 --> E

    E --> F["Decision Engine"]

    subgraph DEC[" "]
        F1["Allow"]
        F2["Warn"]
        F3["Block"]
        F4["Auto-Redact"]
    end

    F --> F1
    F --> F2
    F --> F3
    F --> F4

    F1 --> G["Audit Logs"]
    F2 --> G
    F3 --> G
    F4 --> G

    G --> H["Admin Dashboard"]

    style A fill:#1E293B,stroke:#64748B,color:#fff
    style B fill:#0F172A,stroke:#3B82F6,color:#fff
    style C fill:#0F172A,stroke:#3B82F6,color:#fff
    style D fill:#0F172A,stroke:#8B5CF6,color:#fff
    style D1 fill:#0F172A,stroke:#EF4444,color:#fff
    style D2 fill:#0F172A,stroke:#3B82F6,color:#fff
    style D3 fill:#0F172A,stroke:#22C55E,color:#fff
    style E fill:#1E293B,stroke:#F59E0B,color:#fff
    style F fill:#1E293B,stroke:#F59E0B,color:#fff
    style F1 fill:#0F172A,stroke:#22C55E,color:#fff
    style F2 fill:#0F172A,stroke:#F59E0B,color:#fff
    style F3 fill:#0F172A,stroke:#EF4444,color:#fff
    style F4 fill:#0F172A,stroke:#06B6D4,color:#fff
    style G fill:#1E293B,stroke:#64748B,color:#fff
    style H fill:#1E293B,stroke:#64748B,color:#fff
```

<br/>

## System components

<table>
<tr>
<td width="33%" valign="top">

### Browser Extension
![Chrome](https://img.shields.io/badge/-Extension-4285F4?style=flat-square&logo=googlechrome&logoColor=white)

The first checkpoint. Before "send" actually does anything, the extension grabs the prompt, holds it, and waits for a verdict.

Captured per request:
- User ID and timestamp
- Browser and destination platform
- The prompt text itself

</td>
<td width="33%" valign="top">

### Backend API
![FastAPI](https://img.shields.io/badge/-FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white)

Built on FastAPI and Python. Handles auth, runs the full detection pipeline, applies org-level policy, computes the risk score, logs the event, and returns a decision.

</td>
<td width="33%" valign="top">

### Audit & Dashboard
![Dashboard](https://img.shields.io/badge/-Analytics-6366F1?style=flat-square&logo=grafana&logoColor=white)

Every scan is logged user, timestamp, destination, score, trigger, decision, override, and policy applied.

Turns raw logs into prompt history, risk analytics, and department-level trends.

</td>
</tr>
</table>

<br/>

## Tech stack

<div align="center">

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![spaCy](https://img.shields.io/badge/spaCy-09A3D5?style=for-the-badge&logo=spacy&logoColor=white)
![Regex](https://img.shields.io/badge/Regex-Rule--Based-64748B?style=for-the-badge&logo=regex101&logoColor=white)
![Chrome Extension](https://img.shields.io/badge/Browser-Extension-4285F4?style=for-the-badge&logo=googlechrome&logoColor=white)

</div>

<br/>

<div align="center">

---

*Built to keep people productive with AI — without handing over the keys.*

</div>
