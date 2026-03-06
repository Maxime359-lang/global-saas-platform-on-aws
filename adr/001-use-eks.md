# ADR-001: Use Amazon EKS as the Kubernetes Platform

## Status
Accepted

## Context
The goal of this portfolio project is to demonstrate a production-oriented platform engineering approach for a global SaaS environment running on AWS.

The platform must showcase:
- Kubernetes-based orchestration
- high availability considerations
- secure infrastructure design
- integration with AWS-native services
- environment consistency across development stages
- a realistic operating model for modern cloud-native workloads

Since AWS is a core platform requirement for the target role, the Kubernetes solution should align closely with AWS-native operational patterns.

---

## Decision
We will use **Amazon EKS** as the Kubernetes platform for this project.

---

## Rationale
EKS was selected because it provides:
- strong alignment with AWS as the target cloud platform
- a managed control plane, reducing operational burden
- integration with IAM and AWS networking primitives
- support for production-grade Kubernetes patterns
- realistic relevance for platform engineering and DevOps roles in AWS-heavy environments
- a credible base for demonstrating GitOps, observability, workload isolation, and scaling patterns

Using EKS allows the project to focus on platform design, workload delivery, and operational architecture rather than spending disproportionate effort on control-plane maintenance.

---

## Alternatives Considered

### 1. Self-managed Kubernetes on EC2
Pros:
- maximum control
- deeper exposure to cluster internals

Cons:
- significantly higher operational complexity
- less aligned with the practical cloud-native direction of the target role
- more time spent on control-plane maintenance than on platform engineering outcomes

### 2. k3s / lightweight local clusters
Pros:
- simpler and cheaper for experimentation
- fast to bootstrap

Cons:
- weaker signal for AWS production-grade platform design
- limited relevance for a senior AWS/Kubernetes role
- not ideal for demonstrating realistic cloud architecture decisions

### 3. ECS instead of Kubernetes
Pros:
- simpler AWS-native container orchestration
- lower operational overhead

Cons:
- does not satisfy the strong Kubernetes requirement
- does not support the Kubernetes-specific design areas expected in the role, such as CNI, CSI, and service mesh considerations

---

## Consequences

### Positive
- strong alignment with AWS + Kubernetes expectations
- realistic foundation for GitOps and CI/CD integration
- credible base for HA, security, and scaling documentation
- enables later extension into service mesh, storage, and observability topics

### Negative
- higher cost than local-only solutions
- still requires meaningful understanding of AWS networking and IAM
- does not expose all control-plane internals as self-managed Kubernetes would

---

## Follow-up Decisions
This decision will require additional ADRs for:
- networking model
- GitOps tooling
- ingress strategy
- service mesh adoption
- storage strategy
- cost optimization approach
