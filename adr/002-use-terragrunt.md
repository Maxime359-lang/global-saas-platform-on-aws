# ADR-002: Use Terraform + Terragrunt for Infrastructure Management

## Status
Accepted

## Context
The target platform must support multiple environments and demonstrate repeatable, structured infrastructure management aligned with platform engineering practices.

The infrastructure layer needs to show:
- modular design
- environment separation
- reuse across dev/stage/prod
- maintainable layout
- clear ownership of infrastructure components
- a realistic GitOps/IaC-friendly operating model

Terraform is a standard choice for declarative infrastructure provisioning, but by itself it can become repetitive and harder to manage across multiple environments without a clear orchestration pattern.

---

## Decision
We will use **Terraform** for infrastructure provisioning and **Terragrunt** to manage environment orchestration and shared configuration.

---

## Rationale
Terraform was chosen because it is a widely used Infrastructure as Code tool and strongly aligns with the target role requirements.

Terragrunt was added because it improves:
- environment composition
- DRY configuration management
- reuse of shared module inputs
- maintainability of multi-environment setups
- clarity of infrastructure structure in platform-oriented repositories

This combination creates a cleaner and more realistic layout for a platform that spans multiple environments and infrastructure components.

---

## Alternatives Considered

### 1. Terraform only
Pros:
- fewer tools
- simpler onboarding for small projects
- direct control over module usage

Cons:
- more duplication between environments
- weaker structure for larger multi-environment repositories
- more manual handling of shared configuration and orchestration

### 2. Pulumi
Pros:
- strong programming model
- good flexibility

Cons:
- weaker alignment with the exact expectations of the role
- less direct signal than Terraform/Terragrunt for this target portfolio

### 3. AWS CDK
Pros:
- native AWS abstraction
- strong developer experience

Cons:
- less aligned with the explicit Terraform/Terragrunt expectation
- reduces direct coverage of a listed requirement

---

## Consequences

### Positive
- strong alignment with the role requirements
- credible multi-environment structure
- cleaner infrastructure composition
- better readability for reviewers
- easier extension into reusable modules and shared patterns

### Negative
- Terragrunt adds another layer to learn and document
- repository structure becomes more opinionated
- requires discipline in module boundaries and naming

---

## Proposed Structure
A high-level structure will follow this pattern:

- `terraform/modules/` for reusable infrastructure modules
- `terraform/live/` for environment-specific orchestration
- separate logical stacks for networking, cluster, registries, and supporting services

---

## Follow-up Decisions
This decision will require additional ADRs for:
- remote state strategy
- module boundaries
- environment naming conventions
- deployment promotion model
- secrets handling approach
