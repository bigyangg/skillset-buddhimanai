---
name: user-research
description: Plan and conduct user research to inform product and design decisions. Use when a user says "user research", "user interviews", "usability testing", "customer discovery", "understand my users", "user feedback", "jobs to be done", "interview script", "affinity mapping", "research synthesis", or "validate my assumptions".
---

# User Research

## What This Does

Design and execute user research studies — from planning and recruiting to conducting interviews, synthesizing insights, and translating findings into product decisions. Covers generative research (discovery) and evaluative research (usability testing).

## Research Types

| Type | Question It Answers | When |
|------|---------------------|------|
| **User interviews** | Why do users behave this way? | Before building, ongoing |
| **Usability testing** | Can users complete this task? | After building a prototype/feature |
| **Survey/quantitative** | How many users have this problem? | After qualitative confirms the pattern |
| **Diary/field study** | What do users do in their natural context? | Complex workflows, long-term behavior |
| **Card sorting** | How do users organize information? | IA/navigation design |

**Always do qualitative (interviews/usability) before quantitative (surveys).** Surveys can only measure things you already know to ask about.

## Non-Negotiables

- **5 users reveal ~85% of usability problems.** You don't need 100 participants for qualitative research.
- **Ask about past behavior, not hypothetical future behavior.** "What did you do last time?" beats "Would you use a feature that...?"
- **Never lead the witness.** Avoid questions that suggest the answer you want to hear.
- **Record with consent, always.** Written consent before recording — voice, video, or notes.
- **Separate data from interpretation.** What they said/did is data. What it means is interpretation. Keep them separate until synthesis.

## User Interview Script Template

```markdown
# User Interview Guide

**Study:** [Product name] — [Research question]
**Duration:** 45–60 minutes
**Interviewer:** [Name]

---

## INTRO (5 min)

"Thank you for taking the time today. I want to learn about your experience with [problem space] — 
not about our product specifically. There are no right or wrong answers; I'm here to learn from you. 
I may take notes. Is it okay if I record this session? 
[Get consent]

Before we start: can you briefly tell me your role and what a typical workday looks like?"

---

## WARM-UP (5 min)

*Goal: Establish rapport, understand their context*

1. "How long have you been doing [relevant activity]?"
2. "What tools do you use day-to-day for [problem domain]?"
3. "Walk me through the last time you [core task]."

---

## CORE QUESTIONS (30 min)

*Goal: Understand their workflow, pain points, and workarounds*

**Workflow:**
4. "Walk me through how you [core task] from start to finish."
   → Follow up: "Then what happens?" "Who else is involved?" "Where does that information come from?"

**Pain points:**
5. "What's the most frustrating part of that process?"
   → Follow up: "How often does that happen?" "What do you do when that happens?"
6. "Is there a step where you feel like you're doing something a workaround should handle?"

**Prior solutions:**
7. "Have you tried other ways to solve [the problem]?"
   → Follow up: "What made you stop using it?" "What did you like about it?"

**Priorities:**
8. "If you could change one thing about how you currently handle [problem], what would it be?"

---

## CLOSING (5–10 min)

9. "Is there anything about [topic] that I haven't asked about but think I should know?"
10. "Is there anyone else you think I should talk to?"

"Thank you so much. This was incredibly helpful. Is it okay if I follow up by email 
if I have a question about something you shared?"

---

## PROBING PHRASES (use throughout)

- "Tell me more about that."
- "Can you give me a specific example?"
- "What happened next?"
- "How did that make you feel?"
- "Why was that important to you?"
- "What did you do when that happened?"
```

## Usability Test Protocol

```markdown
# Usability Test Guide

**Goal:** Evaluate whether users can [specific task] using [prototype/feature]
**Participants:** 5–8 users matching [persona criteria]
**Duration:** 30–45 minutes

---

## TASKS

Write tasks as goals, not instructions. Never mention UI elements.

✅ Goal-oriented task:
"You just signed up for the app. Please add your first team member."

❌ UI-instructed task (leads the user):
"Click the 'Members' tab, then click 'Add Member' and fill in the form."

---

## TASK LIST

| # | Task | Success Criteria | Time Limit |
|---|------|-----------------|-----------|
| 1 | Find your invoice from last month | Downloads correct invoice | 3 min |
| 2 | Add a team member with editor access | User added with correct role | 4 min |
| 3 | Set up a monthly billing reminder | Reminder visible in settings | 5 min |

---

## DURING THE SESSION

- "Please think out loud — say what you're thinking as you go."
- Do NOT help them unless they completely give up (wait at least 2 minutes of struggle)
- Note: what they click, where they pause, what they say, where they look confused
- After each task: "On a scale of 1–7, how easy or difficult was that?"
```

## Research Synthesis (Affinity Mapping)

```
1. DUMP: Write every observation on a sticky note (one per note)
   - Use verbatim quotes ("I always have to ask my manager...")
   - Use observations ("User immediately scrolled past the hero CTA")
   - Use interpretations SEPARATELY (label: "interp: user didn't notice CTA")

2. CLUSTER: Group related notes together
   - First pass: by similarity (related behaviors, similar quotes)
   - Second pass: by theme (name the pattern you see in each cluster)

3. NAME: Write a theme statement for each cluster
   - Format: "[Users] [verb] [when/because/despite] [condition]"
   - ✅ "Users skip the tutorial because they expect to learn by doing"
   - ❌ "Tutorial" (too vague — not a finding)

4. PRIORITIZE: Rank clusters by frequency + severity
   - Frequency: How many participants mentioned it?
   - Severity: How much does it block their goal?
```

## Research Report Template

```markdown
# Research Findings — [Study Name]

**Date:** [Date range]
**Method:** [User interviews / Usability test]
**Participants:** [N] × [persona description]
**Researcher:** [Name]

---

## Key Findings

### Finding 1: [Headline as an insight, not a description]

> "Verbatim quote from a participant that captures this finding."
> — Participant #3, Product Manager, 5 years experience

**What we observed:** [Fact-based description of behavior]
**Why it matters:** [Impact on user goal / product metric]
**Frequency:** Mentioned by 4 of 5 participants

---

### Finding 2: ...

---

## Recommendations

| Finding | Recommendation | Priority | Effort |
|---------|---------------|----------|--------|
| #1 | Add inline explanation to [field] | High | Low |
| #2 | Restructure onboarding flow | High | High |
| #3 | Fix error message for [scenario] | Medium | Low |

---

## What We Got Wrong (Assumptions Disproved)

- We assumed users would configure settings first → They skip directly to the core action
- We assumed the free tier was acceptable → 4/5 said they'd leave if limited to 3 projects

---

## Open Questions for Next Study

- [ ] Do power users (3+ years) face the same problems as new users?
- [ ] What triggers a user to upgrade vs. churn?
```

## Recruiting Participants

```markdown
## Screener Survey Template

1. What is your primary job role? [dropdown]
2. How often do you [core task]? 
   - Daily / Weekly / Monthly / Rarely
3. Which tools do you currently use for [problem domain]? [multi-select]
4. How long have you been using [relevant tool/workflow]?
   - < 6 months / 6–12 months / 1–3 years / 3+ years
5. [Optional disqualifier] Do you work at a company that [competing product/specific context]? 
   - Yes / No → If yes, disqualify

## Incentives (Standard Rates)
- 30-min interview: $25–$50 gift card
- 60-min interview: $50–$100 gift card
- Usability test: $50–$75 gift card
```

## Ethical Guidelines

- Always get **informed consent** before recording
- Allow participants to **stop at any time** without consequence
- **Anonymize** all quotes in reports (Participant #3, not "Jane from Acme Corp")
- **Never share** participant PII with external stakeholders
- Don't interview **direct competitors' employees** without legal guidance
