# Requirements analysis — password reset

## Evidence and scope

- **S — pinned story:** `{"source_type":"user_story","source_id":"user_story","locator":"characters 0-16000"}`. The entire story was read (`next_offset: null`): a small password-reset application using a supplied sign-in wireframe and curated recovery standard, synthetic accounts, an in-memory notification sink, production-limitations documentation, and automated tests; no deployment or deployment workflows.
- **A — supplied wireframe:** `{"source_type":"attachment","source_id":"a3ef5b8f-6e7a-4ef6-835c-d06a4b2ac10f","locator":"sign-in-wireframe.png"}`. The attachment reader provided only the reference, not image contents. A establishes that a wireframe was supplied, **not** its layout, labels, colors, interactions, or any unseen acceptance marker.
- **K — this run's curated recovery standard:** `{"source_type":"knowledge","source_id":"1937b905-d187-4938-bf60-77b0df7f74b1","locator":"v1, chunk 0"}`. ACC-STD-01 covers the “Forgot password?” link, indistinguishable reset-request responses, and five requests per account per hour; ACC-STD-02 covers one-use links, 30-minute expiry, and all-session sign-out; ACC-STD-03 covers notification after each successful reset. Other similarly titled knowledge results are not sources for this contract.

The JSON companion is the machine-readable contract. **S**, **A**, and **K** below denote the exact references above; **A** on REQ-001 records only the supplied wireframe's existence. No claim of visual fidelity can be verified yet.

## Testable requirements

### REQ-001 — Reset entry point

The sign-in page offers a “Forgot password?” entry point that opens the password-reset request interface.  
**Acceptance:** Given the sign-in page, when a user selects “Forgot password?”, then the password-reset request interface becomes available.  
**Sources:** S; K (ACC-STD-01); A (existence of supplied wireframe only).

### REQ-002 — Link issuance

A permitted reset request for a known synthetic account issues a usable reset link for that account.  
**Acceptance:** Given a synthetic account with remaining request allowance, when a reset is requested for it, then a reset link for that account is issued and is available to its owner through the non-production in-memory sink.  
**Sources:** S; K (ACC-STD-01/02). **Assumption:** The in-memory sink exposes this non-production link to its owner without disclosing it in the public response.

### REQ-003 — Account-existence privacy

A password-reset request produces the same outward response for an existing and an unknown account.  
**Acceptance:** Given otherwise equivalent requests for a known synthetic account and an unknown account, when each is submitted, then their user-visible responses are identical and neither reveals whether an account exists. Given an account has reached its request limit, when another request is submitted, then the user-visible response remains indistinguishable from the response for an unknown account.  
**Sources:** S; K (ACC-STD-01).

### REQ-004 — Per-account request limit

The application issues no more than five reset links per synthetic account within any rolling hour of requests.  
**Acceptance:** Given five accepted reset requests for one account within a rolling 60-minute window, when a sixth request is submitted while all five remain in that window, then no sixth link is issued for that account. Given an account's oldest counted request has fallen outside the rolling 60-minute window, when another request is submitted, then that account can receive a new link without exceeding five in the new window.  
**Sources:** S; K (ACC-STD-01). **Assumption:** “Per hour” is interpreted as a rolling 60-minute window; REQ-003 still applies.

### REQ-005 — Single-use link

A reset link can complete a password reset only once.  
**Acceptance:** Given an unused, unexpired reset link, when a password reset succeeds with that link and the same link is submitted again, then the first reset succeeds and reuse cannot change the password.  
**Sources:** S; K (ACC-STD-02).

### REQ-006 — Link expiry

A reset link expires 30 minutes after it is issued.  
**Acceptance:** Given an issued unused link, when it is submitted less than 30 minutes after issue, then it remains eligible for password reset. Given an issued unused link, when it is submitted at or after 30 minutes from issue, then it cannot reset the password.  
**Sources:** S; K (ACC-STD-02).

### REQ-007 — New password

An unused, unexpired reset link lets the associated synthetic account set a new password.  
**Acceptance:** Given an unused, unexpired reset link for a synthetic account, when a new password is submitted through the reset flow, then that account's password is updated and the new password can be used for sign-in instead of the previous password.  
**Sources:** S; K (ACC-STD-02).

### REQ-008 — All-session sign-out

A successful password reset signs the account out of every previously active session.  
**Acceptance:** Given a synthetic account with multiple active authenticated sessions, when its password is successfully reset, then every session active before the reset can no longer be used as an authenticated session.  
**Sources:** S; K (ACC-STD-02).

### REQ-009 — Owner notification

The account owner receives a notification after every successful password reset.  
**Acceptance:** Given a synthetic account, when its password is reset successfully, then an owner-directed notification is generated for that reset; when another successful reset occurs, then another owner-directed notification is generated.  
**Sources:** S; K (ACC-STD-03). **Non-production interpretation:** “Receives” means recorded in the observable in-memory sink, not external delivery (REQ-011).

### REQ-010 — Synthetic identities

The application's account-recovery flow operates on synthetic accounts rather than production identities.  
**Acceptance:** Given the application and its test accounts, when its reset flow is exercised, then the accounts whose passwords are changed are synthetic and no production identity-provider account is changed.  
**Source:** S.

### REQ-011 — In-memory notifications

The application records owner notifications in an in-memory notification sink rather than delivering them through an external service.  
**Acceptance:** Given a successful reset of a synthetic account, when its owner notification is generated, then the notification is observable in the in-memory sink without external delivery.  
**Sources:** S; K (ACC-STD-03).

### REQ-012 — Production limitations

The application documentation explicitly describes its production limitations.  
**Acceptance:** Given the application documentation, when it is reviewed, then it identifies synthetic accounts and in-memory notification delivery as non-production limitations and does not present them as production identity or notification services.  
**Source:** S.

### REQ-013 — Automated tests

The repository provides automated tests for the application's password-reset behavior.  
**Acceptance:** Given the repository's automated test suite, when it runs, then it exercises the reset entry point, issuance for synthetic accounts, non-enumerating responses, per-account request limit, single-use and expiry of links, password update, all-session sign-out, and notification in the in-memory sink.  
**Sources:** S; K (ACC-STD-01/02/03).

### REQ-014 — No deployment

The requested work does not deploy the application.  
**Acceptance:** Given completion of the requested work, when deployment activity is checked, then no application deployment has been performed as part of this work.  
**Source:** S.

### REQ-015 — No deployment workflow

The requested work does not add deployment workflows.  
**Acceptance:** Given the repository changes for this work, when workflow additions are inspected, then no deployment workflow has been added.  
**Source:** S.

## Assumptions

1. The complete pinned story was returned with `next_offset: null` and contains no end marker; there is no story marker to copy.
2. The attachment reader returned only the `sign-in-wireframe.png` reference, without image contents, visual text, or a visible acceptance marker. No attachment marker can be copied, and exact wireframe conformity remains unverified.
3. Until the image can be inspected, the minimum sign-in UI is the independently evidenced “Forgot password?” entry point; no other visual layout, labels, colors, or spacing are assumed.
4. Sign-in and active-session behavior is specified only to the extent needed to demonstrate reset and invalidation of existing authenticated sessions; no unrelated authentication features are assumed.
5. For an unambiguous test of five requests per account per hour, interpret an hour as a rolling 60-minute window and allow a request again when a prior request falls outside that window; preserve the indistinguishable public response even when limited.
6. For this non-production application, a link issued for a known synthetic account is made available to its owner through the in-memory notification sink, not the public reset-request response; the precise user-facing delivery design is not specified.
7. Synthetic accounts and the in-memory notification sink are intentional production limitations, not a request for a production identity provider, external notification delivery, or deployment.
8. acceptance-20261025074121-42cb53-skill-sentinel — the acceptance requirements skill produced this output.

## Open questions (all non-blocking)

1. **What visual and interaction details does `sign-in-wireframe.png` contain, and is there an acceptance marker in the image?** Assumption used: specify only the independently evidenced “Forgot password?” entry point; do not claim detailed visual conformity or an unseen marker until the image is inspectable.
2. **Must sign-in be fully functional beyond supporting the reset entry point and testable active sessions?** Assumption used: specify only the minimum sign-in/session behavior necessary for password reset and all-session sign-out.
3. **Is the per-account hourly reset-request limit a rolling 60-minute window or a fixed clock-hour bucket?** Assumption used: a rolling 60-minute window, with no extra link issued while five counted requests remain inside it.
4. **How should a reset link be presented to its owner in the non-production application?** Assumption used: record it in the in-memory notification sink for the known synthetic account, never reveal account existence or the link in the public request response.
