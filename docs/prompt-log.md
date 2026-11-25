
# PayLink - AI Prompt Log

## Entry 1: Problem Summary

- **Date:** 2026-11-22
- **Author:** Sama Hany
- **Stage:** Requirements (1. Problem Summary)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt:**

  > Write the Problem Summary for requirements.md.
  >
  > **Length:** 3 to 4 short paragraphs, about 180 words.
  >
  > **It must cover only:**
  > - what PayLink is, who pays through it (mobile app or agents), and how PayLink earns money;
  > - the main problem: PayLink holds customers' money, so it must never charge a customer twice or charge for a payment the biller did not receive;
  > - that the system must return money correctly when a payment fails, support reconciliation (checking PayLink's records against the billers' records), and prevent the same payment from being processed twice even if a request is repeated or a biller replies late;
  > - that billers behave differently (fast, slow, delayed, daily files);
  > - the overall goal.
  >
  > The first time you mention the utility companies, write "utility companies (billers)".
  >
  > Do not include detail that belongs elsewhere: payment states, hold rules, ledger, configuration, user roles.
  >
  > **Output format:** only the paragraphs.

- **Summary of answer:** Four short paragraphs:
  - what PayLink is and how it earns
  - the money-holding problem
  - refunds, reconciliation and duplicate prevention
  - the different biller behaviours and the overall goal
- **Verdict and why:** **Accepted.** The summary matches sections 2.1 and 2.4 of the brief, covers all the points I asked for, and has no technical details.

---

## Entry 2: Stakeholders

- **Date:** 2026-11-22
- **Author:** Sama Hany
- **Stage:** Requirements (2. Stakeholders)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt:**

  > Write the Stakeholders section as a Markdown table with columns: Stakeholder | Role | What they care about .
  >
  > Include only parties that appear in the PDF or are clearly implied, and that are stakeholders of the PayLink system itself. Use these where the PDF supports them: customers, agents, admins, support employees, the billers (ElectroCo, AquaCo, TopUpNet, NetCo), the SMS/notification provider, PayLink management, regulators, and the development team.
  >
  > **Rules:**
  > - Do not include course instructors, TAs or anyone who only grades the project.
  > - Every row needs specific page numbers, checked against the PDF.
  > - "What they care about" must be a real concern supported by the PDF, one or two short sentences. Never write "not described in the PDF" in the table.
  > - If you are unsure a stakeholder is supported, do not put it in the table; list it under "Questions for the team".
  >
  > **Output format:** the table, a short "Questions for the team" list.

- **Summary of answer:** A 12-row table (customers, agents, admins, support employees, the four billers, SMS provider, management, regulators, development team) , plus 4 open questions about regulators, who upgrades verification, a possible fraud team, and a card provider.
- **Verdict and why:** **Fixed.** "I merged the four biller rows into one, because they are the same type of stakeholder; the behaviour of each biller belongs in the user stories and refined the description of Billers' interests to make it clearer and more accurate .I clarified the description of External Authorities to better explain their role in examining PayLink's handling of customers' money.""

---
## Entry 3: User Stories FR-1 to FR-10 (first answer had problems)

- **Date:** 2026-11-22
- **Author:** Sama Hany
- **Stage:** Requirements (3. User Stories)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt (original):**

  > Write user stories for FR-1 to FR-10 (I will change this range each time), one story per FR.
  >
  > Format for each story:
  > - Title line: "US-XX: Title (FR-X, Priority)"
  > - "As a [role], I want to [goal], so that [benefit]."
  > - Acceptance criteria as "Given ... When ... Then ..." (at least 2 per story, each testable with a clear yes/no result)
  >
  > **Rules:**
  > - Priority must be exactly the one in the PDF's requirements table (Must / Should / Could). Never lower a Must.
  > - Use the exact numbers from the PDF when a criterion needs them (limits, fees, timeouts, retries). Money in EGP with exact decimals.
  > - A payment in the Unknown state is never refunded automatically.
  > - Describe behavior only, no implementation details.
  >
  > **Output format:** the stories, a short list of anything unclear (with page numbers).

- **Summary of the wrong answer:** Ten stories (US-01 to US-10) with correct priorities and numbers, but three parts were not supported by the brief:
  - US-06 had a criterion about enabling a biller again, but FR-6 only covers add, edit and disable.
  - US-08 had the label "(test value)" inside a criterion.
  - US-10 had a criterion about different idempotency keys, which the PDF does not state.
- **Why it was wrong:** The brief says to use only information from the PDF. These three parts were assumptions added by the AI.
- **Verdict and why:** **Fixed.** I kept the stories and asked for a correction of the three points only.

## Entry 4: User Stories FR-1 to FR-10 (improved prompt)

- **Date:** 2026-11-22
- **Author:** Sama Hany
- **Stage:** Requirements (3. User Stories)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt (improved):**

  > Please fix these four points only. Do not change anything else.
  > 1. US-06: remove the fourth criterion (enabling a biller again). FR-6 only says add, edit and disable.
  > 2. US-08: remove "(test value)" from the mobile top-up criterion.
  > 3. US-10: remove the third criterion (different idempotency keys), because the PDF does not state it. Add it to the unclear points instead.
  > 4. US-10: replace the first criterion with this one: "Given a payment request has already been sent using a given idempotency key, When the same request with the same key is sent again, Then the original result is returned without creating a second payment." Also change the second criterion to: "Given the repeated request in the previous case, When the wallet is checked, Then only one payment exists for that key, with no second hold and no second charge." Reason: FR-10 and hard case 1 (page 6) apply whatever state the first payment is in, so the first criterion must not say "completed".
  >
  > Output format: the revised stories and the updated unclear points only.

- **Summary of the better answer:** US-06, US-08 and US-10 were corrected as requested. US-10 now has two criteria that apply whatever state the first payment is in. The unclear points list was updated.
- **Verdict and why:** **Accepted.** I checked the corrected stories against the brief (FR-6, FR-10 and hard case 1 on page 6) and nothing else was modified.
---

## Entry 5: User Stories FR-11 to FR-19

- **Date:** 2026-11-22
- **Author:** Sama Hany
- **Stage:** Requirements (3. User Stories)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt:**

  > Write user stories for FR-11 to FR-19, one story per FR.
  >
  > Format for each story:
  > - Title line: "US-XX: Title (FR-X, Priority)"
  > - "As a [role], I want to [goal], so that [benefit]."
  > - Acceptance criteria as "Given ... When ... Then ..." (at least 2 per story, each testable with a clear yes/no result)
  >
  > **Rules:**
  > - Priority must be exactly the one in the PDF's requirements table (Must / Should / Could). Never lower a Must.
  > - Use the exact numbers from the PDF when a criterion needs them (limits, fees, timeouts, retries). Money in EGP with exact decimals.
  > - A payment in the Unknown state is never refunded automatically.
  > - Describe behavior only, no implementation details.
  >
  > **Output format:** the stories, a short list of anything unclear (with page numbers)w.

- **Summary of answer:** Nine stories (US-11 to US-19) with 4 to 9 criteria each. FR-17 and FR-19 are Should and the rest are Must. Thirteen unclear points are listed with page numbers.
- **Verdict and why:** **Fixed.** The stories matched the brief (priorities, fee and commission calculations, limits, timeouts and retry times), but three criteria needed more precision (see Entry 6).

---

## Entry 6: User Stories FR-11 to FR-19 (improved prompt)

- **Date:** 2026-11-22
- **Author:** Sama Hany
- **Stage:** Requirements (3. User Stories)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt (improved):**

  > Revise only the user stories below. Do not change anything else, and do not add requirements that are not in the PDF.
  > 1. US-12: in the first criterion, state that the wallet starts with 1,000.00 EGP available and 0.00 EGP held.
  > 2. US-14: in the second criterion, show both transitions: the "failed" callback makes the payment Failed, and when the hold is released the payment becomes Refunded.
  > 3. US-18: add one criterion: "Given a verified account with enough funds and no payments today, When it confirms a payment of exactly 5.00 EGP, Then the payment is not rejected for being below the minimum."
  >
  > Output format: the revised stories only (US-12, US-14, US-18).

- **Summary of the better answer:** US-12 now states the starting balances, US-14 shows Failed followed by Refunded, and US-18 has a boundary test for exactly 5.00 EGP. Everything else was unchanged.
- **Verdict and why:** **Accepted.** I checked the three changes against the life cycle table and the limits on page 5. The US-14 criterion was less precise than the life cycle, which says Failed becomes Refunded only after the hold is released.
---

## Entry 7: User Stories FR-1 to FR-19 (review and corrections)

- **Date:** 2026-11-22
- **Author:** Sama Hany
- **Stage:** Requirements (3. User Stories)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt:**

  > Review the user stories from US-01 to US-19 against the official PayLink Year Work Project - Student Brief.
  >
  > Identify only necessary corrections. Do not rewrite correct stories or add requirements that are not in the PDF.
  >
  > Check the actors, acceptance criteria, numerical examples, payment lifecycle transitions, timeout scope and retry schedule.
  >
  > Distinguish between requirements explicitly stated in the PDF and derived acceptance criteria. Keep the original priorities and do not change unrelated content.
  >
  > Provide the necessary corrections only, with a short explanation of why each correction is needed.

- **Summary of the answer:** The review identified several corrections and clarifications:
  1. US-01: Remove the criterion requiring the basic verification level to be displayed in the customer's profile, because the PDF does not explicitly require this.
  2. US-04: Remove "and method" from the history criterion, because the PDF does not explicitly require displaying the payment method in the history.
  3. US-05: Change the story actor from Customer to PayLink System, because recording balance changes in the ledger is a system responsibility.
  4. US-09: Clarify that the 10-second timeout applies to a live biller only, not all billers.
  5. US-11: Change the story actor from Customer to PayLink System, because the system controls payment state transitions.
  6. US-12: Clarify the starting wallet balances in the criterion about a biller refusing a payment, so the expected balance after releasing the hold can be tested clearly.
  7. US-13: Clarify that the retry times (1, 5 and 15 minutes) are measured from the moment the payment enters Unknown, with no more than three re-queries.
  8. US-06: Keep the remaining criteria, but treat behavior not explicitly stated in FR-6 as derived acceptance criteria rather than direct requirements from the PDF.
- **Verdict and why:** **Accepted.** The review improved the accuracy and testability of the user stories while keeping them aligned with the official brief. It also distinguished explicitly stated requirements from derived acceptance criteria without presenting assumptions as direct PDF requirements.

## Entry 8: User Stories FR-20 to FR-28 and hard cases (first answer had a problem)

- **Date:** 2026-11-22
- **Author:** Sama Hany
- **Stage:** Requirements (3. User Stories)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt (original):**

  > Write user stories for FR-20 to FR-28, one story per FR.
  >
  > Format for each story:
  > - Title line: "US-XX: Title (FR-X, Priority)"
  > - "As a [role], I want to [goal], so that [benefit]."
  > - Acceptance criteria as "Given ... When ... Then ..." (at least 2 per story, each testable with a clear yes/no result)
  >
  > **Rules:**
  > - Priority must be exactly the one in the PDF's requirements table (Must / Should / Could). Never lower a Must.
  > - Use the exact numbers from the PDF when a criterion needs them (limits, fees, timeouts, retries). Money in EGP with exact decimals.
  > - A payment in the Unknown state is never refunded automatically.
  > - Describe behavior only, no implementation details.
  >
  > After these, also write extra stories for hard cases from section 2.7 that no FR story already covers: case 4 (two simultaneous payments, balance covers only one), case 6 (duplicate callback ignored and logged), and case 7 (settlement file lists a payment PayLink has as Failed). Link each to its hard case number.
  >
  > **Output format:** the stories, a short list of anything unclear (with page numbers).

- **Summary of the wrong answer:** Twelve stories (US-20 to US-31) with correct priorities, and the conflict in FR-27 was reported as an unclear point. One part was not supported by the brief: US-31 had a fourth criterion about a Refunded payment listed as received, but hard case 7 only covers a payment that PayLink has as Failed.
- **Why it was wrong:** The rules say to use only information from the PDF. This criterion was an assumption added by the AI.
- **Verdict and why:** **Fixed.** I kept all the stories and asked for a correction of US-31 only.

## Entry 9: User Stories FR-20 to FR-28 and hard cases (improved prompt)

- **Date:** 2026-11-22
- **Author:** Sama Hany
- **Stage:** Requirements (3. User Stories)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt (improved):**

  > Revise only US-31. Remove the fourth criterion (a Refunded payment listed as received), because hard case 7 in the PDF only covers a payment that PayLink has as Failed. Add it to the unclear points instead. Change nothing else.
  >
  > Output format: the revised US-31, the new unclear point.

- **Summary of the better answer:** US-31 now has three criteria, all based on hard case 7. The Refunded case was moved to the unclear points. Nothing else was changed.
- **Verdict and why:** **Accepted.** I checked US-31 against hard case 7 on page 6 and nothing else was modified.

---

## Entry 10: Non-Functional Requirements

- **Date:** 2026-11-22
- **Author:** Sama Hany
- **Stage:** Requirements (4. Non-Functional Requirements)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt:**

  Write at least 6 measurable non-functional requirements as a Markdown table: ID | Category | Requirement | Target number | How it is measured | Priority.
Base them on the quality targets table in section 3.3 of the PDF (correctness, performance, resilience, modifiability, auditability, testability), using its exact targets.
Do not add targets that are not in the PDF. If you think another is useful, list it separately as a suggestion.
No vague wording like "fast" or "secure". Every row needs a number or a pass/fail test.
Output format: the table, the separate suggestions list.

- **Summary of answer:** A table of 6 requirements (NFR-1 to NFR-6), with correctness merged into one charges and negative balances. All are Must, with a note that this priority is the team's decision. Six suggestions are listed separately.
- **Verdict and why:** **Accepted.** I checked every target against the quality table on page 10  and found nothing invented. Every row has a number or a pass/fail test.

---

## Entry 11: Questions, ambiguities and decisions

- **Date:** 2026-11-22
- **Author:** Sama Hany
- **Stage:** Requirements (5. Questions, ambiguities and decisions)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt:**

  Write the "Questions, ambiguities and decisions" section for requirements.md as a Markdown table with these columns: # | Question or ambiguity | Why it is unclear (page number) | Options | Decision | Reason.

Rules:
- List only ambiguities that really exist in the PDF. Do not invent any. Every row needs a specific page number checked against the PDF.
- Put "TBD by team" in the Decision and Reason columns, because my team decides.
- Give 2 or 3 short options for each question.
- Merge duplicates: if the same question appeared in more than one place, write it once.
- Keep only questions that change the design or the tests. Leave out tiny wording details.
- Do not add implementation details.

Check especially these topics:
1. Daily limit: does it count the service fee, does held money count, and is the day a calendar day or a rolling 24 hours (pages 2 and 5)?
2. Do the 5 EGP minimum and 10,000 EGP maximum apply to the bill amount or to the total with fee (page 5)?
3. When an agent pays for a customer, whose wallet is charged (pages 2, 5 and 9)?
4. What is the agent commission when a promotion makes the fee 0 (pages 5 and 9)?
5. During the 10-minute block, which payments are refused, does the sixth payment itself count, and does support unblocking end the block early (pages 5 and 9)?
6. If a payment is Unknown and later confirmed, when is the agent commission paid (pages 4, 5 and 9)?
7. Do the re-query times (1, 5 and 15 minutes) count from the moment the payment became Unknown or from the previous re-query (pages 5 and 6)?
8. Does a refund return the service fee, can it be partial, and who approves it (page 9)?
9. How is the 1.5% top-up fee rounded when it has more than two decimals (pages 5 and 6)?
10. Who upgrades a customer to verified (page 8)?
11. Which notification channels are required: the module list names SMS, email and push, but FR-22 names SMS and push (pages 5, 7 and 9)?
12. Which state is a TopUpNet payment in between "accepted" and the callback, and how long does it wait before becoming Unknown (pages 4 and 5)?
13. How does a customer see amount, fee and total for NetCo, which has no live inquiry (pages 4 and 8)?
14. Is the 200 EGP internet tier based on the bill amount or the total, and are the promotion start and end dates inclusive (page 5)?
15. What does "full history" contain in FR-20, and what does "payments" mean in the daily totals of FR-28: a count, an amount, or both (page 9)?
16. The hard cases have no priority in the PDF. Which priority do we use, given that FR-26 (reconciliation) is Should but hard case 7 needs it (pages 6, 9, 12 and 15)?

Also check for contradictions inside the PDF, especially between FR-27 (refund of a completed payment, page 9) and the payment life cycle in section 2.5, where Completed is final and Refunded follows only Failed (pages 4 and 5). Write this one as its own row and explain the conflict.

Output format: the table only, then one Prompt Log row (Date | Author | Stage | Tool and model | Prompt | Summary of answer | Verdict and why). Leave Author and Verdict empty for me.

- **Summary of answer:** A 25-row table with options for each question and "TBD by team" in the Decision and Reason columns. The daily limit and velocity topics are split into two rows each. The contradiction between FR-27 and the life cycle (Completed is final) is its own row (row 25). The AI also corrected a wrong page reference in my prompt (the re-query rule is on page 5 and FR-13 on page 8).
- **Verdict and why:** **Accepted.** I checked the page numbers and the ambiguities against the brief, and found nothing invented. Row 25 matches page 5 (Completed is final, Refunded follows only Failed) and FR-27 on page 9.




| Date | Author | Stage | Tool and Model | Prompt | Summary of Answer | Verdict (accepted, fixed, rejected) and Why |
|---|---|---|---|---|---|---|
| 2026-11-25 | Member 3 | Design - Use Case Diagram | ChatGPT - GPT-6 | Act as a software engineering analyst. Based on the PayLink requirements, create a detailed PlantUML use case diagram. Include Customer, Agent, Admin, Support Employee, Biller and Notification Provider. Group related use cases into packages and use include/extend relationships where appropriate. | Generated a use case diagram with actors, packages, use cases and include/extend relationships. | Fixed: the initial diagram needed improvements in its UML relationships. I reviewed the relationships and corrected unnecessary or incorrect connections to make the diagram more consistent with the system requirements. |
| 2026-11-25 | Member 3 | Design - Payment State Diagram | ChatGPT - GPT-6 | Create a PlantUML state diagram for PayLink payments. Include Initiated, FundsHeld, SentToBiller, Completed, Failed, Unknown, Rejected and Refunded. Follow the allowed transitions in the project requirements. Unknown payments must not be refunded automatically. | Generated the payment lifecycle and its transitions. The initial version included the main states but needed to represent the refund process from Failed. | Fixed: I checked the diagram against the requirements and ensured that Failed transitions to Refunded only after the held amount is released. I also ensured that Completed, Rejected and Refunded are final states. |
| 2026-11-25 | Member 3 | Design - State Diagram Layout | ChatGPT - GPT-6 | Improve the layout of the payment state diagram. The transition labels from Initiated to FundsHeld and Rejected overlap. Separate the two paths clearly without changing the payment lifecycle logic. | The first generated layout had overlapping transition labels and a Final State note covering the arrows. The next version introduced a decision diamond, but the layout still did not solve the overlap. | Fixed: I rejected the first two layouts because they did not solve the visual problem. I improved the prompt by requiring the arrows to go in opposite directions, with Rejected on the left and FundsHeld on the right, and increased spacing. |
| 2026-11-25 | Member 3 | Design - Sequence Diagram: Refund a Failed Payment | ChatGPT - GPT-6 | Create a PlantUML sequence diagram for refunding a failed PayLink payment. Include Biller, Biller Adapter, Payment Service, Wallet Service, Ledger and Notification Service. Show the held amount being released before the payment becomes Refunded. | Generated a sequence diagram showing the biller refusal, payment failure, wallet release, ledger entry and customer notification. | Fixed: the initial diagram showed the Notification Service notifying the Customer directly. I corrected the interaction so the external SMS/Push Provider delivers the notification to the Customer. |
| 2026-11-25 | Member 3 | Design - Diagram Validation | ChatGPT - GPT-6 | Review my PayLink payment state diagram and failed-payment refund sequence diagram against the official project requirements. Identify incorrect transitions, missing steps and inconsistencies. Do not invent new requirements. | Reviewed the diagrams and identified improvements related to payment state transitions, wallet release, ledger recording and notification delivery. | Accepted with corrections: I used the review to check consistency between the two diagrams and retained the changes that matched the project requirements. |


