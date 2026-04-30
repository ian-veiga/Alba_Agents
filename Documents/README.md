# 🦉 ALBA — AI-Powered Productivity Assistant for Student Entrepreneurs

> **A**ssistente **L**inear entre **B**em-estar e **A**utogestão  
> Multi-agent WhatsApp assistant that helps student entrepreneurs balance academic life and business — built with HCD methodology.

[![Status](https://img.shields.io/badge/status-active-brightgreen)](.)
[![Stack](https://img.shields.io/badge/stack-n8n%20%7C%20OpenAI%20%7C%20Firebase%20%7C%20Redis-blue)](.)
[![License](https://img.shields.io/badge/license-MIT-orange)](.)

---

## 🧠 What is ALBA?

ALBA is a multi-agent AI system designed for students who also run their own businesses. Through a natural WhatsApp conversation, ALBA helps users:

- Organize their academic and entrepreneurial routines
- Set and track goals with automatic reminders
- Get AI-powered time management suggestions
- Balance productivity and well-being

The system was conceived using **Human-Centered Design (HCD)** methodology and is aligned with **SDG 8 — Decent Work and Economic Growth**.

---

## 🏗️ Architecture Overview

```
WhatsApp (User)
      │
      ▼
Evolution API (Webhook)
      │
      ▼
n8n Orchestration Layer
  ├── Security & Routing
  ├── Message Type Handler (text / audio / image / video)
  ├── Humanization Layer (message batching with Redis)
  │
  ▼
AI Agent (GPT-4.1-mini + LangChain)
  ├── Redis Chat Memory (session context, TTL: 24h)
  ├── [TOOL] Criar Meta → Firestore
  │
  ▼
Response Splitter (natural WhatsApp message batching)
      │
      ▼
Evolution API (Send response back to user)
```

---

## 🔧 Tech Stack

| Layer | Technology |
|---|---|
| Orchestration | [n8n](https://n8n.io/) |
| LLM | OpenAI GPT-4.1-mini |
| Memory | Redis (session + temp message buffer) |
| Database | Google Firebase Firestore |
| WhatsApp API | Evolution API |
| Image Analysis | GPT-4o-mini Vision |
| Methodology | Human-Centered Design (HCD) |

---

## ⚙️ Core Features

### 🤖 AI Agent — ALBA
A conversational agent with a structured onboarding flow:

1. **Step 01** — Welcome & name collection
2. **Step 02** — Academic context (institution, course, shift)
3. **Step 03** — Entrepreneurial context (business, pain points, routine)
4. **Step 04** — Goal setting (academic + business priorities)

Persona: friendly, young, slightly Recife-accented Portuguese. Concise messages, max 400 characters per block.

### 📋 Goal Management (Firestore Tool)
When a user defines a goal in natural language, the agent automatically:
- Extracts title, category (`academico` / `empreendedor` / `bemestar`), priority, and deadline
- Validates and stores the goal in Firestore
- Confirms in conversational language — no technical jargon shown to the user

### 💬 Humanization Layer
Messages are split and sent in natural WhatsApp-style chunks with delays between them — simulating how a human would type. Uses Redis as a temporary message buffer to aggregate rapid successive messages from the user before processing.

### 🖼️ Multi-modal Input
| Type | Handling |
|---|---|
| Text | Direct processing |
| Audio | Speech-to-text via Evolution API |
| Image | GPT-4o-mini vision analysis |
| Video | Rejected with friendly redirect to audio |
| Document / Sticker | Gracefully handled |

### 🔐 Security
- API key validation on every webhook request
- Human takeover mode (Redis flag `alba:atendimento_humano`) that pauses the agent for up to 1 hour
- `/reset` command to clear conversation memory

---

## 📁 Repository Structure

```
Alba_Agents/
├── workflows/
│   ├── Alba.json                  # Main orchestration workflow (n8n)
│   └── Criar_Meta_Firestore.json  # Goal creation sub-workflow (n8n tool)
├── docs/
│   └── Artigo_2VA.docx            # Academic paper (HCD methodology)
└── README.md
```

---

## 🚀 Getting Started

### Prerequisites

- [n8n](https://n8n.io/) (self-hosted or cloud)
- OpenAI API key
- Evolution API instance (WhatsApp)
- Firebase project with Firestore enabled
- Redis instance

### Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/ian-veiga/Alba_Agents.git
   cd Alba_Agents
   ```

2. **Import workflows into n8n**
   - Import `workflows/Criar_Meta_Firestore.json` first (sub-workflow)
   - Import `workflows/Alba.json` (main workflow)
   - Note the sub-workflow ID and update the `Call '[TOOL] Criar Meta Firestore'` node if needed

3. **Configure credentials in n8n**
   | Credential | Used for |
   |---|---|
   | `openAiApi` | GPT-4.1-mini + GPT-4o-mini |
   | `googleFirebaseCloudFirestoreOAuth2Api` | Firestore read/write |
   | `redis` | Session memory + message buffer |

4. **Configure your Evolution API**
   - Set the webhook URL to your n8n webhook endpoint
   - Enable `speechToText` for audio transcription

5. **Update the security key**
   - In the `Security check` node, replace the hardcoded API key with your Evolution API key

6. **Activate both workflows** and test by messaging your WhatsApp number.

---

## 🔑 Environment Variables (n8n Credentials)

All sensitive values are managed through n8n's native credentials manager. No `.env` file is required.

---

## 🗺️ Roadmap

- [ ] Weekly progress reports sent proactively via WhatsApp
- [ ] Mobile app dashboard (Figma prototype → React Native)
- [ ] Gamification system for goal completion
- [ ] Personalized AI feedback based on historical patterns
- [ ] Finance-focused chatbot module
- [ ] Pricing calculator tool for entrepreneur users

---

## 📄 Academic Context

This system was developed as part of a research project at **UFRPE (Universidade Federal Rural de Pernambuco)** — Bacharelado em Sistemas de Informação. The full paper describing the HCD methodology, user research, prototyping, and evaluation is available in `/docs/`.

**Authors:** Ian De Lima Veiga, Carlos Jonathan de Lima Malta, Emanuel Henrique Silva Nascimento, Maycon Romario Dos Santos Pereira, Letícia Batista Rodrigues da Silva, Luiz Eduardo Nascimento de Souza, Yonara Mirelly Araújo da Silva, Roberta Macêdo Marques Gouveia, Elizabeth Regina Tschá

---

## 🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

---

## 📬 Contact

**Ian Veiga** — [ian.veiga@ufrpe.br](mailto:ian.veiga@ufrpe.br) · [GitHub @ian-veiga](https://github.com/ian-veiga)
