# Security Architecture

## Purpose
This document describes the security baseline of the reference AWS and Kubernetes platform.

Its goal is to show how security is built into the platform design across infrastructure, Kubernetes workloads, CI/CD, secrets handling, and operational practices.

This is a reference security architecture for a portfolio platform. It is intended to demonstrate secure-by-default platform thinking rather than claim the full maturity of a large enterprise security program.

---

## Security Goals
The platform security model is designed to achieve the following goals:

- reduce unnecessary exposure of infrastructure and workloads
- enforce clear access boundaries
- apply least privilege wherever practical
- protect secrets and sensitive configuration
- improve trust in the delivery pipeline
- separate environments and reduce blast radius
- support future policy enforcement and security automation

---

## Security Design Principles

### 1. Secure by default
The platform should default to private, restricted, and controlled behavior unless exposure is explicitly required.

### 2. Least privilege
Permissions should be scoped to the minimum level needed for a workload, user, or automation component to perform its task.

### 3. Separation of concerns
Infrastructure access, application deployment, and runtime permissions should be treated as separate concerns.

### 4. Defense in depth
Security should exist at multiple layers, including AWS boundaries, Kubernetes boundaries, CI/CD validation, and operational controls.

### 5. Traceable change management
Security-sensitive changes should be version-controlled, reviewable, and consistent with Git-based workflows.

### 6. Environment isolation
Development, staging, and production should be clearly separated to reduce risk and accidental cross-environment impact.

---

## Security Scope
This security model covers the following major areas:

- AWS identity and access boundaries
- Kubernetes workload isolation
- secret handling direction
- image and dependency scanning
- CI/CD security controls
- environment separation
- policy enforcement direction
- operational security awareness

---

## AWS Security Direction
The AWS layer should provide the first major security boundary for the platform.

Key security expectations include:

- use of IAM-based access control
- scoped permissions for infrastructure components
- reduced public exposure of internal resources
- separation between public-facing and private platform components
- controlled access to supporting services such as container registry and DNS management

The AWS foundation should support a model where the platform is private by default and exposure is deliberate.

---

## IAM and Access Control
Identity and access management is a central part of the platform security model.

The platform direction assumes:

- distinct roles for infrastructure provisioning, deployment, and runtime access
- least privilege as the default permission model
- reduced use of broad administrative permissions
- explicit mapping between responsibility and access

Examples of access boundaries to consider:

- Terraform execution role
- GitLab CI deployment role
- Kubernetes workload identity
- read-only operational access where appropriate

This helps reduce privilege sprawl and improves clarity around who or what can do what.

---

## Kubernetes Workload Security
Workloads running on Kubernetes should not be treated as automatically trusted.

The security baseline assumes:

- namespace-based separation
- controlled service exposure
- careful handling of workload permissions
- future policy enforcement for pod and manifest behavior
- separation between externally exposed workloads and internal-only components

Kubernetes should be treated as an execution platform with explicit trust boundaries, not a flat trusted zone.

---

## IRSA and Workload Identity Direction
The platform is expected to use a workload identity model that avoids unnecessary static credentials.

A preferred direction in AWS-based Kubernetes environments is to map workload permissions to dedicated identities rather than embedding long-lived credentials inside workloads.

This improves:

- access control clarity
- credential handling
- permission scoping
- auditability

A detailed implementation direction will be documented in dedicated decisions and infrastructure examples.

---

## Secret Management Direction
Secrets handling is a critical security concern.

The platform direction assumes:

- secrets should not be hardcoded in source control
- workloads should receive secrets through approved runtime mechanisms
- environment separation should apply to secrets as well
- secret access should be limited to the workloads and services that need it

This portfolio platform is intended to document the direction and operational expectations around secrets, even if the final implementation evolves over time.

---

## CI/CD Security
The delivery pipeline is part of the security boundary.

The GitLab CI/CD direction should include:

- controlled pipeline stages
- image scanning
- dependency scanning
- validation before deployment
- reduced trust in ad-hoc manual changes
- traceable artifact creation

This helps ensure that delivery is not just fast, but also trustworthy.

---

## Image Security
Container images should be treated as security-sensitive artifacts.

The platform should demonstrate awareness of:

- image provenance
- vulnerability scanning
- dependency exposure
- private registry usage
- reducing unnecessary packages and attack surface

A production-oriented platform should not treat container images as opaque build outputs.

---

## Policy Enforcement Direction
The platform should evolve toward policy-based controls rather than relying only on convention.

Areas suitable for policy enforcement include:

- workload security requirements
- manifest validation
- namespace restrictions
- deployment guardrails
- environment-specific controls

This direction may be implemented later through tools such as admission control or policy-as-code solutions, but the platform should already frame security in those terms.

---

## Environment Isolation
Environment isolation is both a security and operational concern.

The platform assumes:

- `dev`, `stage`, and `prod` remain logically separate
- production changes receive the strongest controls
- credentials and secrets are not shared carelessly across environments
- non-production does not become a backdoor into production

This is important for reducing blast radius and maintaining platform trust.

---

## Service Exposure Model
Not every workload should be reachable from outside the platform.

The service exposure model assumes:

- only selected user-facing services are exposed through ingress
- internal services remain private
- backend components such as PostgreSQL and Redis are not exposed publicly
- exposure decisions should be intentional and documented

This helps keep the platform attack surface smaller and easier to reason about.

---

## Operational Security Concerns
Security also includes operational behavior.

Important operational concerns include:

- who can make infrastructure changes
- who can deploy to which environment
- how sensitive changes are reviewed
- how incidents involving access or exposure are handled
- how configuration drift is reduced

This is especially important in platform engineering, where small access mistakes can affect many workloads.

---

## Supply Chain Security Direction
A mature platform should also consider the security of the software supply chain.

Relevant concerns include:

- trusted build process
- artifact traceability
- dependency risk awareness
- image signing or provenance direction
- reducing unreviewed manual artifact handling

This document establishes supply chain security as part of the platform direction even if full implementation comes later.

---

## Logging, Audit, and Traceability
Security depends on being able to understand what changed and how.

The platform should support:

- traceable infrastructure changes
- traceable deployment activity
- reviewable configuration history
- operational visibility into security-relevant actions

This aligns well with Git-based workflows and supports future incident investigation.

---

## Backup and Recovery Security Considerations
Security is also connected to recovery.

Relevant concerns include:

- protecting backup data
- controlling restore access
- ensuring recovery processes do not bypass access controls
- keeping disaster recovery procedures aligned with security boundaries

This is important because recovery workflows can become hidden security weaknesses if they are not designed carefully.

---

## Cost and Security Trade-offs
Security choices can affect cost and complexity.

Examples include:

- stronger isolation can increase infrastructure overhead
- deeper scanning can increase pipeline time
- stricter controls can increase implementation complexity
- more auditability can increase storage and observability costs

The platform should acknowledge these trade-offs instead of pretending security is free.

---

## Future Extensions
This security model is intended to evolve with:

- clearer secret management implementation
- policy-as-code examples
- admission control examples
- stronger workload identity examples
- supply chain security enhancements
- more explicit environment guardrails
- incident response and access review documents

---

## Known Limitations
This document describes a reference security direction rather than a full enterprise security program.

It demonstrates:
- secure-by-default thinking
- awareness of access boundaries
- CI/CD security awareness
- workload and environment isolation principles

It does not yet define a complete compliance framework, full audit architecture, or production-grade security operations program.

---

## Summary
The platform security model is based on a simple principle: keep access narrow, make exposure explicit, treat CI/CD and runtime identity as part of the security boundary, and build platform controls that reduce risk over time.

This creates a credible security baseline for an AWS and Kubernetes platform designed for long-term evolution.
