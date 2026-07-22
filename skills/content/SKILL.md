---
name: Premium UX Writing & Product Copy Guidelines
description: Rules and principles for shipping clear, human, and context-sensitive product microcopy, onboarding flows, and errors that avoid default generic SaaS template fillers.
---

# UX Writing & Product Copy: Senior Lead Guidelines

## Role & Framing
Approach every string in the interface like a Product Writer / UX Content Designer at a top-tier product company (e.g., Slack, Airbnb, Stripe, or Apple style). You are the protector of the user's trust, energy, and time, not a filler of text boxes. Your goal is to write copy that is highly invisible—acting as an intuitive map guiding users through the interface. Clean out all generic SaaS marketing fluff and code-heavy system-speak; replace it with precise, human, and actionable directions.

---

## 1. Core Writing Principles

### Principle A: User-Side Nomenclature
Write from the user's side of the screen. Identify interface objects and controls by what they mean to the human user, not how they are configured in our engineering database.
*   *Incorrect:* "Configure Webhook Payload Endpoint" (Robotic database logic)
*   *Correct:* "Notifications" or "Developer settings" (User mental model)
*   *Incorrect:* "Query index database"
*   *Correct:* "Search"

### Principle B: Active Voice & Predictable Action
Keep microcopy active. Buttons, headers, and navigation links must state exactly what happens when triggered.
*   *Incorrect:* "Submit" or "Process Request"
*   *Correct:* "Save changes," "Create account," "Send email," "Delete teammate"

### Principle C: Flow-Level Vocab Consistency
Do not shift vocabulary mid-flow. Once an entity tag or action is selected, preserve it completely from start to end-state confirmation.
*   *Incorrect:* Button: "Publish article" -> Dialog: "Article will go live now" -> Alert: "Post Updated" (Mixes publish, live, post, update)
*   *Correct:* Button: "Publish article" -> Dialog: "Publish this article now?" -> Alert: "Article published"

### Principle D: Specificity Over Cleverness
Never compromise reading comprehension for a joke or cutesy phrasing. An interface is a utility tools. Forced puns and casual humor generate cognitive friction and alienate international locales.
*   *Incorrect:* "Oops! That's a fail. Let's make this puppy right!"
*   *Correct:* "Enter a valid email address."

---

## 2. Microcopy Rules

Every string in a form, dialog, or drawer must have exactly **ONE** job:
1.  **Form Labels:** Say what the field requires. (Keep under 3 words. Sentence case. e.g. "Work email").
2.  **Helper Text:** Explains *why* we request it or how it will be used. (Placed underneath the input box. e.g. "We'll send your confirmation receipt here").
3.  **Placeholder Text:** Demonstrates an example of the input content format. (Fades out when user types. e.g. "jane.doe@work.com"). Do not use placeholders to state requirements (e.g. "Must contain 8 characters")—that is the helper text's job.
4.  **Tooltips:** Expand on a specific label or icon when requested, pointing at complex edge cases.

### Casing Convention
Use **sentence case** everywhere (only capitalize the first letter of the first word and proper nouns). Never use title case or uppercase styling in standard microcopy blocks unless specifically demanded by dynamic visual design structures (such as tracked metadata labels).
*   *Incorrect:* "Save And Create Account"
*   *Correct:* "Save and create account"

---

## 3. Error & Empty State Writing

### Error Writing Formula
An error message must never just announce a failure. It must explain:
1.  **What happened:** (State the current system state, not the raw error code).
2.  **Why it happened:** (Explain what condition triggered the boundary limit).
3.  **How to fix it:** (Give a direct, immediate, and actionable path to resolution).
4.  *Voice Constraint:* Never say "Something went wrong" (which implies we don't know). Do not write "We apologize for the inconvenience" (falsely apologetic fluff that wastes reading time).

*Example:*
*   *Incorrect:* "Error 500: Database connection failure. Back-end stack crashed."
*   *Correct:* "Unable to upload video. The file size exceeds the 50MB limit. Compress your video or select a smaller file."

### Empty State Invitation
Empty states must never end in a hard stop. They should act as an active invitation to get started:
1.  **State the empty condition:** (e.g. "No teammates yet").
2.  **Detail the benefit of action:** (e.g., "Add colleagues to start collaborating on tasks and sharing feedback").
3.  **Provide a primary Call-to-Action:** (e.g., button labeled "Invite teammate").

---

## 4. Onboarding Flow Writing

First-run onboarding requires a delicate balance. The interface must build momentum without demanding heavy cognitive effort.

### Principles of Onboarding Copy
*   **Progressive Disclosure:** Don't explain every feature at once. Introduce details of complex dashboards only when the user navigates there naturally.
*   **The Power of One:** Each card or pop-up segment must contain only **ONE** focus idea. If a step introduces a feature, tell the user exactly what it does *right now*.
*   **Momentum Phrasing:** Focus on what the user achieves rather than the action they are performing. Show clear progress metrics (e.g., "Step 2 of 3").
*   **Respect Returning Users:** onboarding must be skippable. Always include a clean, accessible "Skip onboarding" or "Skip for now" option that dismisses the walkthrough instantly.

---

## 5. Tone Calibration

Calibrate the tone based on the user's emotional context. Avoid constant high-energy hype.

| User Context | Target Tone | Rules | Example Case |
| :--- | :--- | :--- | :--- |
| **Marketing / Entry** | Playful, Aspiring | Focus on benefits; use warm, engaging language. | *Before:* "System registers users quickly."<br>*After:* "Set up your workspace in minutes." |
| **Onboarding** | Welcoming, Encouraging | Build confidence; explain value immediately. | *Before:* "Setup database now."<br>*After:* "Connect your data source to instantly generate dashboard metrics." |
| **Standard Tasks** | Neutral, Frictionless | Clean utility; keep words as short as possible. | *Before:* "Press to initiate document conversion."<br>*After:* "Convert document" |
| **Errors / Alerts** | Direct, Precise | Solid, calm typography; state resolving steps. | *Before:* "Whoops! You broke our server."<br>*After:* "Invalid billing address. Check your postal code and try again." |

---

## 6. Length Discipline

Default to the shortest sentence that conveys the complete context. To maintain screen balance:
*   **Buttons:** Max `4 words` (`2 words` preferred).
*   **Page Headers:** Max `6 words`.
*   **Form Helpers:** Max `14 words` (should fit on a single line).
*   *Risk-Based Exceptions:* If an action is destructive or irreversible (e.g., clearing a project database), you must increase length limits to explain exactly what is going to be lost and why.

---

## 7. Verification & Self-Critique Checklist
Review all product text snippets through this checklist before integration:

*   [ ] **The Zero-Context Test:** Can a user navigating our application for the first time understand the button's action with no additional instruction?
*   [ ] **The SaaS Fluff Audit:** Have we removed all generic marketing jargon like "seamlessly," "effortlessly," "empower," and "unlock"?
*   [ ] **Naming Consistency:** Are we naming the exact same action/object using matching words throughout the flow?
*   [ ] **Error Resolution:** Does every invalid flow validation state a clear path to fix the error?
*   [ ] **Sentence Case Check:** Do all helper blocks, labels, and text fields follow the sentence casing schema?
*   [ ] **Review standard:** Would this copy survive a strict review by the UX Content Leads at a world-class consumer product studio?

---

_Adapted from: https://github.com/hoodini/ai-agents-skills (MIT License)_
