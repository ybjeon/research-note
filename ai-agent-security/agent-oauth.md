# Agent Authentication & Authorization

## 2LO vs 3LO: recommended approach for Agents

[Security: About 2LO and 3LO](/security/oauth.md#2lo--3lo) — the difference is in the number of parties involved in authentication. 3LO involves a user, while 2LO does not.

### Differences Between 3LO and 2LO

| Category | 3LO (Three-Legged) | 2LO (Two-Legged) |
|---|---|---|
| Participating Parties | User + Client + Server | Client + Server |
| Whose Authority | Access on behalf of **user** | Access using **app's own** authority |
| Consent Screen | Present (browser redirect) | Absent |
| Representative Grant | Authorization Code | Client Credentials |
| Use Case | "Allow reading my Google Calendar" | Server-to-server batch, internal API |
| What Token Represents | Delegated user authority | App's inherent authority |

The core difference is **whose authority is used for access**. 3LO is a structure where users delegate part of their authority to an app, while 2LO operates from the app's own authority from the start.

---

### Why 3LO is Recommended for Agents

When an Agent (an LLM system that autonomously invokes tools) accesses external services, 2LO may seem more appealing — no consent screen, and a single token is enough. Even so, 3LO is recommended for these reasons.

**1) Authority scope is limited per user**

A 3LO token contains only "the scope this user authorized." Even if the Agent malfunctions or is compromised, the impact is bounded by **that user's delegated authority**. A 2LO token carries the app's full authority, so the blast radius in an incident is far larger.

**2) Audit and tracking are clear**

In 3LO, the token records "on behalf of which user" an action was performed. It's possible to track "what was done and under whose authority" separately in logs. In 2LO, all actions are lumped under one subject — "the app" — obscuring whose task it actually was.

**3) Explicit consent gives users control**

In 3LO, users directly view scopes, grant or deny permission, and can later revoke access. This control is fundamental to trust when Agents handle user data. In 2LO, there is no point at which users participate.

**4) Aligns with the least privilege principle**

Agents typically act on behalf of specific users for specific tasks. It follows that they should receive only the authority that user needs for those tasks. 3LO's delegation model fits this structure naturally.

### AWS and Google Cloud Recommendations

AWS and Google Cloud advise using 3LO for agentic workflows accessing user-specific external resources.

- AWS: https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/identity-terminology.html
- Google Cloud: https://docs.cloud.google.com/iam/docs/auth-with-3lo

AWS designates managed 3LO as the recommended authentication method for personal access, but also considers 2LO a legitimate pattern for machine-to-machine or application-level access without user delegation — because many contexts such as IAM roles, service accounts, and machine-to-machine authentication are **appropriately people-free**.

```
- Server-to-server internal API calls
- Background batch / cron jobs
- Infrastructure automation (Agent handles system resources, not user data)
```

In short, use 3LO if an Agent **acts on behalf of a specific user**, and 2LO if it **operates as the system itself** (infrastructure tasks that do not access user data). Neither is absolutely better; the criterion is **whose authority the Agent exercises**.

---

## 4. Summary

```
3LO = User + Client + Server  →  User authority delegation, consent screen present
2LO = Client + Server         →  App's own authority, consent screen absent
```

For Agents handling user data, 3LO is the baseline in terms of authority restriction, audit, and user control. For system tasks that require no human involvement, 2LO is a justified choice as AWS suggests. The decision criterion is always **"whose authority does this token represent?"** In multi-user environments like enterprises, 3LO is the right choice; in single-user or machine-to-machine scenarios, 2LO is appropriate.
