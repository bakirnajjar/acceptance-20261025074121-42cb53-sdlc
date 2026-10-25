# Requirements analysis — working outline

This is a candidate matrix for the later requirements contract, not the final contract or a product deliverable. Visual fidelity to the supplied wireframe cannot yet be assessed.

## Evidence and traceability

- **S** — `{"source_type":"user_story","source_id":"user_story","locator":"characters 0-16000"}`: complete pinned story; `next_offset: null`.
- **A** — `{"source_type":"attachment","source_id":"a3ef5b8f-6e7a-4ef6-835c-d06a4b2ac10f","locator":"sign-in-wireframe.png"}`: attachment reference only; image contents were not rendered by the reader.
- **K** — `{"source_type":"knowledge","source_id":"1937b905-d187-4938-bf60-77b0df7f74b1","locator":"v1, chunk 0"}`: this run's curated account-recovery standard, ACC-STD-01 through ACC-STD-03. Similarly titled results from other runs are **not** evidence for this outline.

## Candidate REQ-NNN matrix

| ID | Atomic candidate requirement | Given / When / Then candidate acceptance | Sources |
| --- | --- | --- | --- |
| REQ-001 | The sign-in page offers a **“Forgot password?”** link that starts a password-reset request. | **Given** the sign-in page, **when** the user selects “Forgot password?”, **then** a reset-request interface is available. | S, K (ACC-STD-01); A establishes only that a wireframe was supplied, not its visible layout |
| REQ-002 | A permitted reset request for a synthetic account issues a usable reset link for that account. | **Given** a synthetic account with remaining request allowance, **when** a reset is requested, **then** a reset link for that account is issued and can be used in the reset flow. | S, K (ACC-STD-01/02) |
| REQ-003 | A reset request gives the same outward response whether the account exists or not. | **Given** matching requests for an existing and an unknown account, **when** each is submitted, **then** their user-visible response is identical and does not disclose account existence. | S, K (ACC-STD-01) |
| REQ-004 | Reset requests are limited to five per account per hour. | **Given** five accepted reset requests for one account within an hour, **when** a sixth is attempted within that hour, **then** no sixth reset link is issued for that account; **when** the allowance becomes available again, **then** a request can be accepted. The user-visible response must still satisfy REQ-003. | S, K (ACC-STD-01) |
| REQ-005 | A reset link can successfully reset the password only once. | **Given** an issued link, **when** it is used to complete a reset and then reused, **then** the first reset succeeds and reuse cannot reset the password. | S, K (ACC-STD-02) |
| REQ-006 | A reset link expires 30 minutes after issue. | **Given** an issued unused link, **when** it is used before 30 minutes elapse, **then** it remains eligible; **when** used at or after 30 minutes, **then** it cannot reset the password. | S, K (ACC-STD-02) |
| REQ-007 | A valid reset link lets its account owner set a new password. | **Given** an unused, unexpired link for a synthetic account, **when** a new password is submitted to complete the reset, **then** the account's password is updated. | S, K (ACC-STD-02) |
| REQ-008 | Completing a reset signs the account out of every active session. | **Given** an account with multiple active sessions, **when** its password is successfully reset, **then** every pre-existing session for that account can no longer be used as an authenticated session. | S, K (ACC-STD-02) |
| REQ-009 | The account owner is notified after every successful password reset. | **Given** a synthetic account, **when** a reset completes successfully, **then** a notification for its owner is recorded for that reset; repeat successful resets each produce an owner notification. | S, K (ACC-STD-03) |
| REQ-010 | The application uses synthetic accounts rather than production identities. | **Given** the running application, **when** its reset flow is exercised, **then** the affected accounts are synthetic, not production identity-provider accounts. | S |
| REQ-011 | Notifications use an in-memory sink. | **Given** a successful reset, **when** its owner notification is generated, **then** it can be observed in the in-memory notification sink without delivering it through an external notification service. | S; K (ACC-STD-03) for the notification behavior |
| REQ-012 | The application documents its production limitations. | **Given** the application documentation, **when** reviewed, **then** it explicitly identifies the synthetic-account and in-memory-notification limitations and does not present the application as a production identity or delivery service. | S |
| REQ-013 | Automated tests cover the password-reset requirements. | **Given** the repository, **when** its automated tests run, **then** they exercise the sign-in reset entry, equal account-existence responses, request limit, link reuse and expiry, password update, session invalidation, owner notification, and synthetic/in-memory constraints. | S; K (ACC-STD-01/02/03) for the standard-derived cases |
| REQ-014 | The work does not deploy the application. | **Given** completion of the requested work, **when** deployment activity is checked, **then** no application deployment was performed. | S |
| REQ-015 | The work does not add deployment workflows. | **Given** the repository changes, **when** workflows are inspected, **then** no deployment workflow has been added. | S |

## Assumptions and non-blocking questions

- **Assumption:** The complete returned story has no visible end marker; `next_offset` is null. No end marker can be copied.
- **Assumption:** The attachment reader returned only reference **A**, not image pixels, visual text, or a visible acceptance marker; therefore there is no verifiable attachment marker to copy and no claim about its exact layout, labels, spacing, or colors. The minimal assumed UI is a sign-in page with the standard's “Forgot password?” reset entry point (REQ-001), without inventing other wireframe details.
- **Question (non-blocking):** What visual and interaction details does `sign-in-wireframe.png` contain, and does it include an acceptance marker? **Assumption used:** Until the image is inspectable, assess only the independently evidenced “Forgot password?” entry point and leave detailed wireframe conformity unresolved.
- **Question (non-blocking):** Must sign-in itself be fully functional beyond supporting the reset entry and testable active sessions? **Assumption used:** Specify only the minimum sign-in/session behavior needed to demonstrate reset and all-session sign-out; do not invent unrelated authentication requirements.
- **Assumption:** Synthetic accounts and in-memory notifications are intentional non-production boundaries, not a request to configure a production identity provider, outbound delivery, or deployment.
- **Assumption:** `acceptance-20261025074121-42cb53-skill-sentinel` — the acceptance requirements skill produced this working outline.

The later contract's pinned schema is `schema_version: "1.0"`, `stage: "requirements_analysis"`, with `source_references`, `assumptions`, `open_questions`, and `requirements`; each final requirement will retain its ID, statement, Given/When/Then acceptance criteria, and source references. This outline does not write that contract.
