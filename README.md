<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:1a1a2e,50:16213e,100:0f3460&height=220&section=header&text=QuickMail%20Backend&fontSize=60&fontColor=ffffff&animation=fadeIn&fontAlignY=38&desc=Spring%20Boot%20API%20powering%20the%20QuickMail%20Gmail%20Extension&descAlignY=58&descSize=18" width="100%"/>

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=22&duration=3000&pause=800&color=4EA8DE&center=true&vCenter=true&width=780&lines=OTP+Auth+%E2%86%92+JWT+%E2%86%92+AI+Draft+Generation;Multi-Provider+AI+Fallback+%3A+Groq+%E2%86%92+Gemini+%E2%86%92+OpenRouter;Production-Style+Backend+for+a+Real+User+Workflow" alt="Typing SVG" />

<br/>

![Java](https://img.shields.io/badge/Java-17-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.x-6DB33F?style=for-the-badge&logo=springboot&logoColor=white)
![Spring Security](https://img.shields.io/badge/Spring%20Security-JWT-6DB33F?style=for-the-badge&logo=springsecurity&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-Persistence-47A248?style=for-the-badge&logo=mongodb&logoColor=white)
![Railway](https://img.shields.io/badge/Railway-Deployment-0B0D0E?style=for-the-badge&logo=railway&logoColor=white)

<br/>

[![Stars](https://img.shields.io/github/stars/bikashcode-dev/QuickMail?style=for-the-badge&color=yellow)](https://github.com/bikashcode-dev/QuickMail/stargazers)
[![Forks](https://img.shields.io/github/forks/bikashcode-dev/QuickMail?style=for-the-badge&color=blue)](https://github.com/bikashcode-dev/QuickMail/forks)
[![Last Commit](https://img.shields.io/github/last-commit/bikashcode-dev/QuickMail?style=for-the-badge&color=orange)](https://github.com/bikashcode-dev/QuickMail/commits)

</div>

QuickMail Backend is the Spring Boot API behind the QuickMail Gmail extension. It handles authentication, OTP verification, JWT-protected access, AI email draft generation, and provider fallback.

This project is designed as a **production-style backend for a real user workflow**.

---

## 📌 Table of Contents

- [Highlights](#-highlights)
- [What The Backend Does](#-what-the-backend-does)
- [High-Level Flow](#-high-level-flow)
- [Authentication Flow](#-authentication-flow)
- [Email Generation Flow](#️-email-generation-flow)
- [AI Fallback Strategy](#-ai-fallback-strategy)
- [Security Approach](#-security-approach)
- [Tech Stack](#️-tech-stack)
- [Project Structure](#-project-structure)
- [Running Locally](#-running-locally)
- [Deployment Notes](#-deployment-notes)
- [Why This Project Matters](#-why-this-project-matters)
- [Future Improvements](#-future-improvements)

---

## ✨ Highlights

<table>
<tr>
<td width="50%" valign="top">

- ☕ Spring Boot 3 + Java 17
- 🔐 Spring Security with stateless JWT authentication
- 📩 OTP-based account verification
- 🍃 MongoDB persistence

</td>
<td width="50%" valign="top">

- 🤖 AI email generation with fallback strategy
- 🚂 Railway-ready deployment setup
- 🧩 Chrome extension integration

</td>
</tr>
</table>

---

## 🛠 What The Backend Does

| Responsibility | Description |
|---|---|
| 📨 OTP Delivery | Sending OTPs for email verification |
| ✅ OTP Verification | Verifying OTPs and activating accounts |
| 🔑 Password Login | Allowing password-based login after verification |
| 🪪 Token Issuance | Issuing JWT tokens for protected API access |
| ✍️ Draft Requests | Receiving email drafting requests from the frontend |
| 🧱 Prompt Building | Building safe, structured prompts |
| 🔀 AI Routing | Routing generation requests through multiple AI providers |
| 📤 Response Delivery | Returning generated email content to the extension |

---

## 🔄 High-Level Flow

```mermaid
flowchart LR
    A[🧩 Chrome Extension] -->|OTP / Login| B[QuickMail Backend]
    B -->|Receives JWT| A
    A -->|Sends protected draft request| B
    B --> C{🔐 Backend validates token}
    C --> D[🧱 Backend builds prompt]
    D --> E[🤖 Backend calls AI provider s]
    E --> F[📤 Draft returned to Gmail]

    style A fill:#1a1a2e,stroke:#4EA8DE,color:#fff
    style B fill:#16213e,stroke:#6DB33F,color:#fff
    style C fill:#0f3460,stroke:#e94560,color:#fff
    style D fill:#16213e,stroke:#6DB33F,color:#fff
    style E fill:#16213e,stroke:#FFD43B,color:#fff
    style F fill:#0f3460,stroke:#4EA8DE,color:#fff
```

---

## 🔐 Authentication Flow

```mermaid
sequenceDiagram
    actor User
    participant Ext as Chrome Extension
    participant BE as QuickMail Backend
    participant DB as MongoDB

    Note over User,DB: 1️⃣ Request OTP
    User->>Ext: Enter email address
    Ext->>BE: Request OTP
    BE->>BE: Normalize email
    BE->>DB: Invalidate older active OTPs
    BE->>BE: Generate fresh OTP
    BE->>DB: Store OTP metadata
    BE-->>User: 📧 OTP sent via mail delivery

    Note over User,DB: 2️⃣ Verify OTP
    User->>Ext: Submit OTP
    Ext->>BE: Verify OTP
    BE->>DB: Check OTP exists
    BE->>BE: Check expiry
    BE->>BE: Check already used
    BE->>DB: Mark user as verified
    BE-->>Ext: ✅ JWT Token

    Note over User,DB: 3️⃣ Password Login (optional, after verification)
    User->>Ext: Email + Password
    Ext->>BE: Login request
    BE->>DB: Validate account
    BE->>BE: Check password hash
    BE-->>Ext: ✅ JWT Token
```

---

## ✍️ Email Generation Flow

```mermaid
flowchart TD
    A[Frontend sends protected request] --> B{🔐 Validate JWT}
    B -->|Invalid| X[🚫 Reject Request]
    B -->|Valid| C[✅ Validate request payload]
    C --> D[🧱 Build prompt based on mode & instruction]
    D --> E[🔀 AI Orchestration Layer]
    E --> F[📤 Return generated text + provider metadata]

    style X fill:#e94560,color:#fff
    style F fill:#4EA8DE,color:#000
```

---

## 🤖 AI Fallback Strategy

The system is designed so **one provider failure does not stop the product**.

```mermaid
flowchart LR
    Req[✍️ Generation Request] --> P1{1️⃣ Groq}
    P1 -->|✅ Success| Out[📤 Draft Returned]
    P1 -->|❌ Fails| P2{2️⃣ Gemini}
    P2 -->|✅ Success| Out
    P2 -->|❌ Fails| P3{3️⃣ OpenRouter}
    P3 -->|✅ Success| Out
    P3 -->|❌ Fails| Err[⚠️ Unusable Response Handling]

    style P1 fill:#16213e,stroke:#4EA8DE,color:#fff
    style P2 fill:#16213e,stroke:#FFD43B,color:#fff
    style P3 fill:#16213e,stroke:#e94560,color:#fff
    style Out fill:#0f3460,stroke:#6DB33F,color:#fff
```

If one provider fails or returns an unusable response, the backend automatically tries the next provider in order: **Groq → Gemini → OpenRouter**.

---

## 🔒 Security Approach

- ✅ Stateless authentication with JWT
- ✅ Protected API routes
- ✅ BCrypt password hashing
- ✅ OTP invalidation and expiry checks
- ✅ Request validation before generation

---

## ⚙️ Tech Stack

<div align="center">

![Java](https://img.shields.io/badge/Java_17-ED8B00?style=flat-square&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-6DB33F?style=flat-square&logo=springboot&logoColor=white)
![Spring Security](https://img.shields.io/badge/Spring_Security-6DB33F?style=flat-square&logo=springsecurity&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=flat-square&logo=mongodb&logoColor=white)
![Maven](https://img.shields.io/badge/Maven-C71A36?style=flat-square&logo=apachemaven&logoColor=white)
![WebClient](https://img.shields.io/badge/WebClient-Reactive-6DB33F?style=flat-square&logo=spring&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-Auth-000000?style=flat-square&logo=jsonwebtokens&logoColor=white)
![Railway](https://img.shields.io/badge/Railway-0B0D0E?style=flat-square&logo=railway&logoColor=white)

</div>

---

## 📁 Project Structure

```text
src/main/java/com/email/emailgen
├── config
├── controller
├── dto
├── exception
├── model
├── repository
├── security
├── service
│   ├── auth
│   ├── impl
│   └── orchestrator
└── EmailgenApplication.java
```

---

## ▶️ Running Locally

```powershell
cmd /c "call mvnw.cmd spring-boot:run"
```

---

## 🚀 Deployment Notes

- 🚂 The backend is structured for deployment on **Railway** and **Render**
- 🔧 Production configuration should come from **environment variables**
- 🚫 Local-only secrets should never be committed
- 🔐 Protected routes depend on valid **JWT tokens**

---

## 🌟 Why This Project Matters

| Capability | Demonstrates |
|---|---|
| 🔐 Real authentication flow | End-to-end OTP + JWT implementation |
| 🛡️ Secure API design | Validation, hashing, token protection |
| 🔗 Frontend-backend integration | Chrome extension ↔ Spring Boot API |
| 🤖 AI orchestration | Multi-provider prompt routing |
| 🔀 Fallback handling | Resilient system design |
| 🚂 Production-style deployment thinking | Environment-driven config, Railway/Render ready |

---

## 🔮 Future Improvements

- [ ] Per-user usage tracking
- [ ] Better observability and metrics
- [ ] Refresh-token strategy
- [ ] Async processing for higher traffic
- [ ] Richer prompt/history management

---

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0f3460,100:1a1a2e&height=120&section=footer" width="100%"/>

</div>
