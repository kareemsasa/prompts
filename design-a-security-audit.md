Design a reusable security-audit prompt that can be sent to an agent operating inside different software projects.

The goal is **not** to audit Themis itself and not to perform a security audit now. Your task is to design the prompt/protocol that a separate project-local agent will later execute.

Treat this as a reusable governance and assurance artifact. It should be portable across repositories without assuming a particular language, framework, hosting provider, deployment architecture, or project maturity.

## Objective

Produce a canonical security-audit prompt that instructs a project-local agent to:

1. discover the project's actual architecture and security-relevant surfaces before judging them;
2. inspect implementation, configuration, dependencies, deployment/runtime boundaries, and relevant documentation;
3. distinguish confirmed vulnerabilities from hardening opportunities, design concerns, unsupported assumptions, and items that could not be verified;
4. reason about exploitability in the project's actual deployment context rather than treating scanner output or theoretical weaknesses as automatically material;
5. collect reproducible evidence for every substantive finding;
6. avoid modifying source, configuration, infrastructure, dependencies, secrets, or live systems unless a later workflow explicitly authorizes remediation;
7. produce a normalized report that can be compared across projects and consumed by Themis for reassessment or follow-up.

## Design requirements

The reusable prompt should be **repository-local but governance-neutral**. The downstream agent may know nothing about Themis and should not need to. Do not make execution depend on Themis-specific files, terminology, commands, schemas, or project registration.

At the same time, design the output so that an external governance layer could reliably interpret it afterward.

The prompt must force the auditor to establish scope before analysis. It should discover, where applicable:

* application components and trust boundaries;
* public and private entry points;
* authentication and authorization mechanisms;
* user-controlled inputs;
* APIs and network listeners;
* data stores and data sensitivity;
* secrets and credential handling;
* file upload/download or filesystem access;
* subprocess or shell execution;
* serialization/deserialization;
* outbound requests and SSRF-relevant behavior;
* browser/client security boundaries;
* dependency and supply-chain exposure;
* containers and images;
* CI/CD and release paths;
* infrastructure and deployment configuration;
* production/runtime differences from the repository;
* logging, monitoring, auditability, and error handling;
* privileged operations;
* administrative surfaces;
* cryptographic usage;
* retention or deletion behavior where security-relevant.

Do not require every category to exist. Require the auditor to explicitly mark categories as applicable, not applicable, or unverified.

## Evidence discipline

Design a strict evidence model.

Every finding should clearly separate:

* **Observation** — what was actually seen;
* **Inference** — what follows from that observation;
* **Security consequence** — what could happen and under what preconditions;
* **Evidence** — exact files, symbols, configuration, commands, test results, runtime observations, scanner records, or documentation supporting the claim;
* **Uncertainty** — anything material that could not be established.

Require the downstream agent to avoid claiming a vulnerability solely because:

* a dependency scanner reports a CVE;
* a package exists in a lockfile;
* a dangerous API exists somewhere in the codebase;
* a configuration looks suspicious in isolation;
* a theoretical attack applies to the technology in general.

It must determine whether the affected code or dependency is reachable, shipped, enabled, exposed, privileged, or otherwise material in the project's actual context whenever that can reasonably be established.

Likewise, absence of a scanner finding must never be treated as evidence that the system is secure.

## Finding taxonomy

Develop a normalized classification scheme that separates at least:

* confirmed security defect/vulnerability;
* probable security defect requiring additional verification;
* security design weakness;
* hardening opportunity;
* dependency/supply-chain concern;
* deployment/configuration concern;
* operational/detection concern;
* documentation or assurance gap;
* unverified surface / evidence gap;
* false positive or non-applicable candidate.

Define severity separately from confidence.

Severity should represent plausible impact and exploitability in the observed context. Use a compact scale such as Critical / High / Medium / Low / Informational, but define what the levels mean rather than relying on labels alone.

Confidence should describe evidentiary certainty independently, for example Confirmed / High / Moderate / Low.

The design should prevent an auditor from laundering uncertainty into severity.

## Threat-model discipline

The prompt should require lightweight threat modeling before findings are finalized.

For important attack surfaces, identify:

* attacker position or required access;
* assets at risk;
* trust boundary crossed;
* prerequisite conditions;
* attack path;
* mitigating controls already present;
* likely impact;
* whether exploitation was demonstrated, inferred, or merely conceivable.

Do not require destructive exploitation to prove a finding.

Explicitly prohibit unsafe proof-of-concept activity against production or third-party systems unless separately authorized. Prefer static reasoning, tests, local reproduction, non-destructive inspection, and existing logs/configuration.

## Runtime and deployment reality

One of the central design requirements is distinguishing repository state from deployed state.

The reusable prompt should instruct the auditor to identify, where evidence permits:

* what is present in source;
* what is built;
* what is packaged;
* what is deployed;
* what is enabled;
* what is reachable;
* what is actually running.

A finding should state which of those layers it applies to.

If production/runtime verification is unavailable, the report must say so rather than infer deployment state from repository contents.

## Existing controls

The audit must record controls that materially reduce risk, not merely defects.

Examples include authentication gates, network isolation, least privilege, container restrictions, input validation, CSP, CSRF defenses, secret isolation, rate limiting, audit logging, runtime omission of vulnerable optional dependencies, or deployment protections.

However, do not turn the report into a generic checklist. Record controls where they affect threat analysis or finding disposition.

## Tooling

Allow project-appropriate use of:

* repository search;
* tests;
* static analysis;
* dependency scanners;
* package-manager audit tools;
* container/image inspection;
* configuration inspection;
* git history when useful;
* safe local execution;
* read-only runtime inspection when authorized and available.

The reusable prompt should explicitly state that automated scanners are evidence sources, not adjudicators.

Require the agent to understand scanner output before promoting it into a finding.

## Safety and mutation boundary

The audit should default to **read-only**.

The downstream agent must not, merely for purposes of the audit:

* upgrade packages;
* rewrite configuration;
* change permissions;
* rotate credentials;
* alter firewall/network rules;
* deploy code;
* restart production services;
* change databases;
* delete data;
* commit changes;
* push branches;
* open or merge pull requests.

If some harmless local artifact is unavoidable, such as scanner caches or temporary build output, require the agent to disclose it.

If the project contains its own operational instructions such as `AGENTS.md`, contribution guidance, security policy, or repository-specific constraints, require the auditor to read and obey them before proceeding.

## Secrets and sensitive data

The prompt must explicitly prevent disclosure of secrets.

Require redaction of:

* credentials;
* tokens;
* private keys;
* session material;
* sensitive customer/user data;
* unnecessary personal information.

The auditor may establish that sensitive material exists or is mishandled without reproducing the secret itself in the report.

## Audit completion criteria

Define when the auditor is allowed to say the audit is complete.

Completion should require:

* architecture/scope discovery performed;
* material attack surfaces considered;
* automated findings adjudicated rather than blindly copied;
* findings supported by evidence;
* severity and confidence assigned independently;
* significant unknowns identified;
* runtime/deployment verification status stated;
* no unauthorized mutations performed;
* final report generated in the required schema.

The auditor should be allowed to conclude that no confirmed vulnerabilities were found. That conclusion must not be phrased as "the application is secure."

## Required report structure

Design a stable output structure suitable for repeated use across projects.

At minimum it should contain:

### 1. Executive verdict

A concise statement of what was audited, whether any confirmed material security defects were found, and the most important qualification on audit coverage.

### 2. Audit scope

What components, repositories, runtime environments, deployment surfaces, and evidence sources were examined.

### 3. Architecture and attack-surface summary

Enough context to understand the findings.

### 4. Existing material controls

Controls that significantly affect the threat model.

### 5. Findings

For each finding include a stable identifier and fields such as:

* title;
* classification;
* severity;
* confidence;
* affected component;
* observed layer: source/build/package/deployment/runtime;
* evidence;
* attack prerequisites;
* impact;
* exploitability/reachability analysis;
* existing mitigations;
* recommended remediation;
* verification needed;
* disposition.

### 6. Scanner/tool results

Summarize tools used and explicitly reconcile important scanner candidates with the actual findings.

### 7. Unverified or inaccessible surfaces

State what could not be inspected and why.

### 8. Recommended next actions

Prioritized, but do not implement them.

### 9. Audit integrity statement

State whether the audit caused any mutations and enumerate them if so.

## Cross-project comparability

Think carefully about what should be standardized and what should remain project-specific.

The final reusable audit prompt should create reports sufficiently normalized that two audits from very different projects can still be compared on:

* material findings;
* severity;
* confidence;
* evidence quality;
* attack surface;
* deployment verification;
* assurance gaps;
* remediation priority.

Do not force false uniformity where technologies differ.

## Design process

Before writing the final reusable prompt, inspect Themis's existing security-review, assessment, validation, deployment, post-deployment-verification, and evidence conventions where relevant. Reuse established concepts when they improve the design, but keep the resulting downstream prompt independent of Themis.

Also inspect prior project security-review records if available. Identify lessons from cases where a scanner finding was technically present but not materially present in production, or where live verification changed the security conclusion. Use those lessons to improve the protocol without embedding project-specific facts into the reusable prompt.

## Deliverables

Return:

1. the proposed reusable security-audit prompt in full, ready to send unchanged or with a small project-context preamble to another agent;
2. a short design rationale explaining the major choices;
3. any fields you believe Themis should later map from the audit report into its own assessment/security-review model;
4. unresolved design questions, if any;
5. a recommendation for where this reusable prompt should live and whether it should be normative, procedural, or a non-governing operator aid.

Do **not** modify the repository yet unless existing repository instructions explicitly define document creation as part of the approved design workflow. If implementation would require a new governing artifact or policy decision, stop at the design and identify that boundary.
