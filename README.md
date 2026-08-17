# Kapture Finance Collections Voicebot — Submission README

## 1. Overview
This submission implements the Kapture Finance outbound collections scenario using a stateful voicebot design. The bot, Maya, authenticates the customer before any debt information is disclosed, identifies intent, uses scoped tools, and records a final disposition.

## 2. Files
- `Kapture_Collections_Voicebot_HLD.pdf` — High-Level Design.
- `Architecture_Diagram.png` — Architecture diagram.
- `System_Prompt.txt` — Final Vapi system prompt.
- `Tool_Schemas.json` — JSON schemas for the tools.
- `Demo_Call_Link.txt` — Placeholder for the actual Vapi recording/Loom link.

## 3. Vapi Setup
Configure one Vapi assistant with:
1. A deliberate LLM/model selection.
2. A conversational voice selected for clear collections dialogue.
3. A speech-to-text/transcriber selected for reliable English speech; Hindi can be enabled for the optional bilingual path.
4. The system prompt from `System_Prompt.txt`.
5. At least three wired tools. Recommended minimum: `verify_customer`, `log_promise_to_pay`, and `send_payment_link`.
6. Mock server/webhook endpoints may be used for the take-home.

## 4. State Enforcement
The most important design choice is that authentication is not only a prompt instruction. The backend/tool layer must enforce it:
- Before verification, debt-related data is unavailable.
- `get_account_details`, `log_promise_to_pay`, and `send_payment_link` require a verified session.
- If the model attempts an unauthorized action, the backend rejects it.

## 5. Demo Scenarios

### Scenario A — Successful Promise to Pay
Expected flow:
CALL_INIT → DISCLOSURE → AUTH_PENDING → VERIFIED → INTENT=WILL_PAY → PTP date/amount extraction → `log_promise_to_pay` → confirmation → `mark_disposition(PTP)`.

Use the assignment's example customer context for the demo where appropriate: Rahul Sharma, personal loan, overdue EMI ₹8,499, 12 days past due.

### Scenario B — Edge Case: Already Paid
Expected flow:
CALL_INIT → DISCLOSURE → AUTH_PENDING → VERIFIED → customer says already paid → verify/check payment status if available → no collection pressure → appropriate resolution → `mark_disposition(ALREADY_PAID)`.

Also test the critical negative path:
A caller who has not completed verification asks, "How much do I owe?" Maya must not reveal the amount.

## 6. What Broke / Debugging Notes
During testing, record actual failures rather than claiming perfect behavior. A useful example to validate is whether the assistant can be talked past authentication. If observed, fix it with explicit state gating and backend tool authorization.

Other useful tests:
- Tool timeout/failure.
- Customer changes intent mid-call.
- Wrong person answers.
- DNC request.
- No-input/voicemail.
- Customer disputes the amount.
- Language switch.

## 7. Design Choices
- Stateful flow: makes the security-critical authentication boundary explicit.
- Scoped tools: each tool has a narrow purpose and structured inputs/outputs.
- Backend enforcement: prevents prompt-only authentication bypass.
- Structured disposition: every call has a machine-readable outcome.
- Mock APIs: keep the take-home build small while demonstrating the integration contract.

## 8. Improvements With More Time
- Production CRM/loan-account integration.
- Stronger identity verification approved by the client.
- Production payment-link integration.
- More comprehensive English/Hindi evaluation.
- Automated conversation test suite and regression evaluation.
- Dashboard for containment, PTP, latency, tool errors, authentication failures, and drop rate.
- Better recovery and fallback handling for telephony/STT/TTS/tool failures.

## 9. Demo Link
Replace the placeholder in `Demo_Call_Link.txt` with the actual Vapi shared call or 2–4 minute Loom URL before submission.
