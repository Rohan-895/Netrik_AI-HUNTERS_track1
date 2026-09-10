# 🤖 AI HR Agent

### Audit-Ready, Deterministic HR Automation Engine

> **Hackathon Track 2 — AI HR Agent**

**Team:** AI Hunters  
**College:** G. Pulla Reddy Engineering College

---

## 👥 Team Members

- **S. Mythili**
- **T. Yaswanth**
- **S. Rohan**
- **T. Kartheek**

---

## 📌 Project Overview

**AI HR Agent** is an intelligent and audit-ready HR automation engine designed to automate key recruitment and employee-management workflows.

The system transforms **resumes, job descriptions, interview availability, leave requests, and HR queries** into structured and explainable decisions while maintaining a complete audit trail.

The primary goal is to reduce manual HR effort while ensuring that important decisions remain **deterministic, explainable, reproducible, and policy-compliant**.

### Core capabilities

- 📄 Resume screening and candidate ranking
- 📅 Constraint-aware interview scheduling
- 🧠 AI-powered structured interview question generation
- 🏖️ Policy-based leave management
- 🚨 HR query escalation and severity detection
- 🔄 Finite State Machine (FSM) for candidate pipeline management
- 📝 Audit logging and explainability
- 📦 Standardized JSON output for automated evaluation

---

# 🏗️ System Architecture

```text
                    ┌─────────────────────┐
                    │    Job Description  │
                    └──────────┬──────────┘
                               │
                               ▼
┌──────────────┐      ┌─────────────────────┐
│   Resumes    │ ───► │ Resume Screening    │
└──────────────┘      │ TF-IDF + Skills +   │
                      │ Experience Scoring  │
                      └──────────┬──────────┘
                                 │
                                 ▼
                      ┌─────────────────────┐
                      │ Candidate Ranking   │
                      └──────────┬──────────┘
                                 │
                                 ▼
                      ┌─────────────────────┐
                      │ Candidate FSM       │
                      │ Pipeline Management │
                      └──────────┬──────────┘
                                 │
                  ┌──────────────┼──────────────┐
                  ▼              ▼              ▼
          ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
          │ Interview   │ │ Question    │ │ Leave       │
          │ Scheduling  │ │ Generator   │ │ Management  │
          └─────────────┘ └─────────────┘ └─────────────┘
                  │              │              │
                  └──────────────┼──────────────┘
                                 ▼
                       ┌──────────────────┐
                       │ Audit & Results  │
                       │    JSON Export   │
                       └──────────────────┘

🚀 Key Features
1. 📄 Deterministic Resume Screening

The resume screening engine evaluates candidates against a Job Description using a deterministic scoring pipeline.

Scoring Formula
Final Score =
    50% × Semantic Score
  + 35% × Skill Score
  + 15% × Experience Score
Technology
TF-IDF Vectorization
Cosine Similarity
Curated skill vocabulary
Required/preferred skill weighting
Experience-based scoring
Required-skill coverage
Explainable scoring breakdown
TF-IDF Configuration
max_features = 6000
ngram_range = (1, 2)
sublinear_tf = True
norm = "l2"

Required skills receive higher importance than preferred skills.

Required Skill  → 3 points
Preferred Skill → 1 point

The system also applies experience and skill-coverage adjustments.

Explainability

Each candidate receives an internal score breakdown containing information such as:

{
  "semantic": 0.72,
  "skill": 0.86,
  "experience": 0.91,
  "coverage": 1.0,
  "bonuses": 0.12,
  "penalties": 0.0
}

This makes candidate ranking easier to understand and audit.

2. 📅 Constraint-Aware Interview Scheduling

The scheduling engine assigns interview slots while considering multiple constraints.

Scheduling constraints
Business hours: 10:00 – 17:30 IST
Asia/Kolkata timezone
Interview duration
10-minute buffer between interviews
Interviewer expertise
Interview type
Interviewer workload
Candidate availability

The scheduler uses deterministic selection and fallback strategies to ensure reproducible results.

Load Balancing

Interviewers with fewer existing bookings are preferred to distribute workload more evenly.

3. 🧠 AI Interview Question Generator

The system generates structured interview questions using an LLM-first architecture.

Primary AI Model

Groq + LLaMA 3.3

Model:
llama-3.3-70b-versatile

When a Groq API key is available, the LLM generates the questions.

If the API is unavailable, the system automatically switches to a deterministic rule-based fallback.

Generated Structure

Exactly 8 questions are generated:

3 × Technical
2 × Behavioral
2 × Situational
1 × Candidate-Specific

Each question contains:

{
  "question": "...",
  "type": "technical",
  "category": "...",
  "difficulty": "medium",
  "evaluation_points": [
    "...",
    "...",
    "...",
    "..."
  ]
}

Every question contains exactly 4 evaluation points.

The generated response is validated before being accepted by the system.

4. 🏖️ Policy-First Leave Management

The leave management module evaluates employee leave requests against organizational policies.

Policy checks
Working-day calculation
Available leave balance
Minimum notice period
Maximum consecutive leave
Leave overlap
Team capacity
Documentation requirements
Policy Priority

The system follows a policy-first approach.

If an ML risk model is available, it provides only an advisory score.
                 Leave Request
                       │
                       ▼
                Policy Validation
                       │
              ┌────────┴────────┐
              │                 │
           Violations        No Violations
              │                 │
              ▼                 ▼
           Reject           ML Advisory
                                │
                                ▼
                             Decision

5. 🚨 HR Escalation Handler

The escalation engine identifies HR queries that require human intervention.

Severity Levels
Level	Examples
🔴 HIGH	Harassment, discrimination, termination, legal issues
🟠 MEDIUM	Compensation, salary revision, policy exceptions, transfers
🟢 LOW	General complaints and feedback

The system also supports compound detection such as:

Harassment + Emotional Distress
            ↓
        HIGH Priority

Escalation decisions are logged for auditability.

6. 🔄 Finite State Machine (FSM)

The candidate hiring pipeline is controlled using a strict Finite State Machine.

Pipeline
APPLIED
   ↓
PROCESSING
   ↓
SHORTLISTED
   ↓
INTERVIEW_SCHEDULED
   ↓
INTERVIEWED
   ↓
SELECTED

or

Any eligible stage
       ↓
    REJECTED
Allowed Transitions
APPLIED
  ├── PROCESSING
  └── REJECTED

PROCESSING
  ├── SHORTLISTED
  └── REJECTED

SHORTLISTED
  ├── INTERVIEW_SCHEDULED
  └── REJECTED

INTERVIEW_SCHEDULED
  ├── INTERVIEWED
  └── REJECTED

INTERVIEWED
  ├── SELECTED
  └── REJECTED
FSM Protections

The system enforces:

Candidate existence validation
Valid transition whitelist
Terminal-state protection
Idempotency checks
Interview booking preconditions
Audit logging

Terminal states:

SELECTED
REJECTED

Once a candidate reaches a terminal state, further transitions are blocked.

📝 Auditability

Every successful FSM transition is recorded in an audit trail.

Example:

{
  "candidate_id": "C001",
  "from": "processing",
  "to": "shortlisted",
  "timestamp": "2026-09-10T10:30:00",
  "reason": "auto_shortlisted"
}

This allows HR administrators and judges to:

Trace candidate decisions
Understand pipeline changes
Replay decision history
Verify system behavior
Investigate unexpected transitions
📦 Hackathon Output Format

The system produces the exact JSON structure required by the hackathon scoring system.

{
  "team_id": "AI_Hunters",
  "track": "track_2_hr_agent",
  "results": {
    "resume_screening": {
      "ranked_candidates": [],
      "scores": []
    },
    "scheduling": {
      "interviews_scheduled": [],
      "conflicts": []
    },
    "questionnaire": {
      "questions": []
    },
    "pipeline": {
      "candidates": {}
    },
    "leave_management": {
      "processed_requests": []
    },
    "escalations": []
  }
}
🛠️ Tech Stack
Technology	Purpose
Python	Core application
Scikit-learn	TF-IDF and cosine similarity
NumPy	Numerical computation
Pandas	Data processing
Groq API	LLM integration
LLaMA 3.3 70B	Interview question generation
Joblib	ML model artifact handling
Python Dataclasses	Structured data models
Python Enum	FSM state management
JSON	Standardized output
Git / GitHub	Version control
🎯 Design Principles

The project was designed around five major principles:

1. Deterministic

The same input should produce reproducible decisions wherever rule-based processing is used.

2. Explainable

Candidate scores and pipeline transitions can be inspected and understood.

3. Auditable

Important actions are recorded with timestamps and transition reasons.

4. Policy-First

HR policy constraints take precedence over advisory ML predictions.

5. Fault-Tolerant

LLM-dependent functionality has deterministic fallback mechanisms.

🌟 What Makes This Project Different?

Traditional HR automation systems often depend heavily on opaque AI decisions.

AI HR Agent takes a hybrid approach:

             AI HR Agent
                  │
       ┌──────────┴──────────┐
       │                     │
   AI / ML Layer         Rule Layer
       │                     │
       ▼                     ▼
   LLaMA 3.3            HR Policies
   TF-IDF               FSM
   ML Advisory          Scheduling Rules
                         Escalation Rules
       │                     │
       └──────────┬──────────┘
                  ▼
        Explainable + Auditable
             HR Decisions

This provides the flexibility of AI while retaining the predictability and control required for HR workflows.

🔮 Future Enhancements

Potential future improvements include:

🌐 Web-based HR dashboard
👤 Role-based HR authentication
📊 Candidate analytics dashboard
📧 Automated email notifications
📅 Google Calendar / Outlook integration
🗄️ Database-backed candidate management
📄 PDF/DOCX resume parsing
🔍 Semantic embeddings for advanced resume matching
🤖 AI-powered HR assistant for employee queries
📈 Advanced recruitment analytics
🔐 Enterprise-grade security and access control
⭐ Keywords

AI HR Automation Recruitment Resume Screening NLP TF-IDF Machine Learning LLM Groq LLaMA Interview Scheduling Leave Management FSM Candidate Pipeline Explainable AI Auditability Python Scikit-learn
