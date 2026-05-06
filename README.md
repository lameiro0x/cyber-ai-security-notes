# 🧠🛡️ Cyber AI Security Notes

> Personal knowledge base about **Cybersecurity + Artificial Intelligence**, focused on **LLM Security**, **AI Safety**, **Guardrails**, **LLMOps**, **AI Red Teaming** and **Purple Team approaches for AI systems**.

---

## 🚀 What is this repository?

This repository documents my learning path in the intersection between **cybersecurity** and **artificial intelligence**.

The main goal is to understand how modern AI systems, especially applications powered by **Large Language Models (LLMs)**, can be:

- 🧪 Tested
- 🛡️ Defended
- ⚠️ Evaluated for risk
- 🔍 Monitored
- 🧱 Protected with guardrails
- 🔁 Improved through continuous testing
- 🧠 Integrated safely into cybersecurity workflows

This is not just a collection of course notes.  
The objective is to build a structured and practical knowledge base that connects **AI concepts** with real **security thinking**.

---

## 🎯 Main objective

The purpose of this repository is to combine my cybersecurity background with a growing specialization in **AI Security**.

I want to move from a mainly offensive security background into a stronger profile around:

- 🧠 AI Security
- 🛡️ Defensive Security
- 🔵 Blue Team
- 🟣 Purple Team
- 🤖 LLM Security
- 🧪 Secure AI testing
- 📊 AI risk evaluation
- 🔐 Secure deployment of AI-powered applications

The final goal is to understand not only how AI systems can fail, but also how to design, evaluate and defend them properly.

---

## 🧭 Learning philosophy

The approach of this repository is based on a simple idea:

```txt
Understand the attack → Measure the risk → Build the defense → Test again
```

In traditional cybersecurity, we usually think about vulnerabilities, threat models, attack paths, controls, logs and mitigations.

In AI Security, the mindset is similar, but the attack surface changes.

Instead of only dealing with SQL injection, XSS, exposed services or weak credentials, AI-powered systems introduce new risks such as:

- Prompt injection
- Jailbreaks
- Model manipulation
- Sensitive data leakage
- Hallucinations
- Unsafe generations
- Insecure tool usage
- Excessive agency
- RAG poisoning
- Weak guardrails
- Poor evaluation pipelines

That is why this repository tries to connect **classic cybersecurity methodology** with the new problems introduced by **LLMs and AI applications**.

---

## 🟣 Why Purple Team for AI?

My preferred approach is **Purple Team AI Security**.

That means combining offensive and defensive thinking.

The goal is not only to learn how an attacker could abuse an AI system, but also how to:

- Detect the attack
- Reduce the impact
- Improve the system
- Validate mitigations
- Create better tests
- Document risks clearly
- Build stronger AI applications

Example:

```txt
Attack:
A user tries to override the system prompt and force the model to ignore its original instructions.

Risk:
The model may reveal internal information, generate unsafe content or perform an unintended action.

Defense:
Apply prompt hardening, input validation, output filtering, guardrails, monitoring and automated testing.

Validation:
Create test cases to check whether the model resists similar prompt injection attempts.
```

This is the type of reasoning I want to develop through this repository.

---

## 📚 Current learning roadmap

The roadmap is organized by learning value, professional recognition and practical relevance for AI Security.

### ✅ Phase 1 — DeepLearning.AI: LLM Safety, Guardrails and LLMOps, ...

Status: **Completed**

This is the first block of the roadmap and the foundation of the repository.

Completed courses:

- ✅ **Safe and Reliable AI via Guardrails**
- ✅ **Quality and Safety for LLM Applications**
- ✅ **Automated Testing for LLMOps**
- ✅ **LLMOps**
- ✅ **Red Teaming LLM Application**


Why this phase matters:

- Strong introduction to reliable AI applications
- Practical view of guardrails and safety layers
- Useful concepts for testing LLM behavior
- Good bridge between AI development and security
- Very valuable for understanding AI application risks

Main topics extracted from this phase:

- Guardrails
- Moderation
- Refusal behavior
- Quality evaluation
- Safety evaluation
- Automated testing
- LLMOps pipelines
- Monitoring
- Feedback loops
- Regression testing for LLM applications

---

### 🔜 Phase 2 — Microsoft: Fundamentals of AI Security

Status: **Planned**

This phase will focus more directly on **defensive AI security**.

Expected value:

- Good professional recognition
- Useful for interviews
- More defensive perspective
- Digital badge available
- Good complement to hands-on LLM notes

Expected topics:

- AI security fundamentals
- Secure AI lifecycle
- Responsible AI
- Threats against AI systems
- Defensive controls
- Data protection
- Risk management
- Security governance

Although Microsoft resources can sometimes be ecosystem-oriented, the goal here is to extract the general security concepts and apply them beyond a single vendor.

---

### 🔜 Phase 3 — OWASP Top 10 for LLM Applications

Status: **Planned**

This will be one of the most important parts of the roadmap.

OWASP is especially valuable because it is:

- Vendor-neutral
- Security-focused
- Recognized by the cybersecurity community
- Directly connected with real LLM application risks
- Very useful for purple team methodology

Expected topics:

- Prompt Injection
- Sensitive Information Disclosure
- Supply Chain Vulnerabilities
- Data and Model Poisoning
- Improper Output Handling
- Excessive Agency
- System Prompt Leakage
- Vector and Embedding Weaknesses
- Misinformation
- Unbounded Consumption

This phase will be used to build practical checklists, risk mappings and defensive controls.

---

### 🔜 Phase 4 — Linux Foundation: AI Risk Management

Status: **Planned**

This phase will add a more strategic and governance-oriented perspective.

Expected value:

- Good recognition
- More neutral and high-level
- Useful for understanding risk frameworks
- Helpful for professional conversations around AI governance

Expected topics:

- AI governance
- Risk management
- Responsible AI
- Accountability
- Compliance
- Organizational controls
- Risk frameworks
- Security and trust in AI systems

This section may be less technical, but it is important to understand how AI Security fits into a broader organizational context.

---

### 🔜 Phase 5 — Giskard AI Security Tutorials

Status: **Planned**

This phase will focus on practical and technical AI Security testing.

Expected value:

- Strong technical orientation
- Useful for hands-on learning
- Good connection with model testing and LLM evaluation
- Practical defensive perspective

Expected topics:

- AI model testing
- LLM evaluation
- Vulnerability detection
- Prompt injection testing
- Robustness checks
- Safety testing
- Automated evaluation workflows

This phase should help transform the notes into more practical labs and experiments.

---

## 🧩 Repository structure

Current structure:

```txt
.
├── Automated Testing for LLMOps/
├── LLMOps/
├── Quality and Safety for LLM Applications/
├── Red Teaming LLM Applications/
├── Safe and reliable AI via guardrails/
├── images/
├── README.md
└── .gitignore
```

The repository is organized mainly by course/resource.

This makes it easier to keep the original learning flow while gradually adding cross-topic notes, checklists and security mappings.

---

## 🔐 Cybersecurity connection

AI Security is not completely separate from traditional cybersecurity.

Many principles are still the same:

- Validate inputs
- Control outputs
- Reduce attack surface
- Apply least privilege
- Monitor behavior
- Log important events
- Review risky actions
- Avoid sensitive data exposure
- Test before deployment
- Design with abuse cases in mind

However, LLMs introduce a different kind of attack surface.

A user can attack the system using natural language.  
A malicious prompt can become an attack vector.  
A model connected to tools can become a decision-making component with real impact.

That is why AI Security requires both:

- Classic cybersecurity thinking
- Understanding of how LLM applications behave

---

## 🧪 Types of notes in this repository

The notes in this repository may include:

- Course summaries
- Key concepts
- Security explanations
- Defensive checklists
- Practical examples
- Risk analysis
- Prompt injection examples
- Guardrail notes
- LLMOps concepts
- Interview preparation notes
- Purple team mappings
- AI Security glossaries

---

## 🧠 Example concepts covered

Some examples of concepts that appear in this repository:

### Probability vs Severity

In LLM safety, it is important to distinguish between how likely something is to be harmful and how severe it would be if it actually happened.

A phrase may look dangerous when analyzed only by keywords, but the context may make it harmless.

This matters because AI safety systems should avoid both:

- False positives: blocking harmless content
- False negatives: allowing genuinely unsafe behavior

---

### Refusal behavior

A refusal happens when a model decides not to answer because the request is unsafe or violates policy.

In AI Security, refusal behavior is important because attackers may try to bypass it through jailbreaks, roleplay, indirect prompts or instruction manipulation.

---

### Guardrails

Guardrails are controls placed around an AI system to reduce unsafe behavior.

They can be applied before the model, after the model or around the full application workflow.

---

### LLMOps regression testing

LLM systems can change behavior when prompts, models, tools or datasets are modified.

Regression testing helps detect when a change breaks safety, reliability or expected behavior.

---

## 🛠️ Practical direction

In the future, this repository can evolve from notes into more practical material, such as:

- AI Security checklists
- Prompt injection test cases
- LLM evaluation templates
- Guardrail design examples
- OWASP LLM mappings
- RAG security notes
- Interview cheat sheets
- Small labs and experiments

The long-term idea is to convert learning into reusable methodology.

---

## 💼 Professional value

This repository is also part of my cybersecurity portfolio.

It shows:

- Interest in an emerging security area
- Continuous learning
- Technical curiosity
- Ability to document complex topics
- Connection between offensive and defensive security
- Understanding of modern AI risks
- Direction towards AI Security and Purple Team work

The goal is to be able to explain these concepts clearly in interviews and demonstrate that my interest in AI Security is practical, structured and real.

---

## 🧾 Disclaimer

This repository is for educational, defensive and research purposes only.

The goal is to understand AI security risks in order to build safer, more reliable and more secure AI-powered systems.

It is not intended to promote unauthorized exploitation, abuse of AI systems or malicious activity.

---

## 👤 Author

Created by **lameiro0x**

Focus areas:

- 🛡️ Cybersecurity
- 🤖 AI Security
- 🧠 LLM Security
- 🟣 Purple Team
- 🔐 Defensive Security
- 🧪 Security Testing
- ⚙️ LLMOps
- 🧱 Guardrails
