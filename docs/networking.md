# Networking Architecture

## Purpose
This document describes the high-level networking model of the reference AWS and Kubernetes platform.

Its goal is to explain how traffic enters the platform, how workloads are isolated, how internal communication is structured, and how the network design supports security, operability, and future growth.

This is a reference networking model for a portfolio platform. It is intended to show sound architectural reasoning rather than reproduce every low-level production detail of a large enterprise environment.

---

## Networking Goals
The networking model is designed to achieve the following goals:

- provide a clear separation between public and private resources
- reduce unnecessary exposure of internal components
- support secure ingress to application workloads
- enable controlled communication between platform components
- align with AWS and Kubernetes networking patterns
- provide a foundation for future policy enforcement and service mesh adoption

---

## High-Level Network Design
The platform is built on top of an AWS VPC that contains both public and private subnets.

At a high level:

- public-facing entry points are exposed through controlled ingress paths
- Kubernetes worker workloads are intended to run in private subnets
- stateful or internal-only services should not be directly exposed to the public internet
- traffic should be routed in a way that keeps the attack surface small and the architecture understandable

This structure supports a common cloud-native pattern where external access is tightly controlled while the majority of platform components remain private.

---

## AWS VPC Model
The AWS networking foundation is based on a dedicated VPC.

The VPC is expected to include:

- public subnets
- private application subnets
- route tables
- internet egress design
- security boundaries aligned with workload exposure

### Public subnets
Public subnets are intended for components that must be reachable from the internet, directly or indirectly.

Typical public-facing elements may include:

- load balancer endpoints
- ingress-related networking components
- selected edge-facing resources

### Private subnets
Private subnets are intended for internal platform components and application workloads.

Typical private elements include:

- Kubernetes worker nodes
- internal application workloads
- internal service-to-service communication paths
- backend connectivity to stateful services

This separation reduces direct exposure and is consistent with a secure-by-default networking model.

---

## Traffic Flow Overview
The intended traffic flow is:

1. an end user resolves the service through DNS
2. traffic is directed to the platform entry point
3. ingress components route traffic to the correct Kubernetes service
4. the target service forwards requests to the appropriate pods
5. application workloads communicate with backend dependencies such as PostgreSQL and Redis over internal paths

This creates a layered model where public access is concentrated at the platform edge and internal communication stays within the private platform network.

---

## North-South Traffic
North-south traffic refers to traffic entering or leaving the platform.

In this architecture, north-south traffic is expected to follow a controlled path:

- DNS resolution through Route53
- ingress through a load balancer and Kubernetes ingress layer
- routing to application services exposed intentionally

Key design principles:

- expose only what is needed
- centralize ingress control
- avoid direct exposure of internal services
- keep user-facing traffic paths easy to understand and operate

---

## East-West Traffic
East-west traffic refers to traffic between workloads inside the platform.

Examples include:

- web application to worker coordination
- web application to Redis
- web application to PostgreSQL
- service-to-service communication inside the cluster

This traffic should be treated as an important design concern, not as a default trust zone.

The platform direction assumes:

- namespace-aware workload separation
- future enforcement through network policies
- controlled communication paths between services
- a path toward stronger service identity and policy enforcement if service mesh is introduced later

---

## Kubernetes Networking Direction
The Kubernetes layer must provide predictable pod-to-pod and service-to-service communication while supporting isolation and security controls.

The networking direction includes:

- cluster-internal service discovery
- service abstraction for stable access
- ingress-based external routing
- network policy readiness
- future compatibility with more advanced traffic control

Detailed implementation choices such as exact CNI behavior will be documented in dedicated architecture decisions.

---

## CNI Considerations
Because the target role expects deep Kubernetes understanding, networking design must acknowledge Container Network Interface (CNI) considerations.

This portfolio platform will document one of the following directions:

- AWS VPC CNI as the AWS-native default networking model
or
- Cilium as an advanced networking and policy-oriented option

The purpose of this section is not to claim final implementation yet, but to demonstrate that pod networking is a design decision, not an afterthought.

Areas to evaluate include:

- IP allocation model
- policy support
- operational complexity
- observability of traffic paths
- compatibility with future service mesh adoption

A final decision will be recorded in an ADR.

---

## Ingress Strategy
Ingress is the main controlled entry point for HTTP/HTTPS application traffic.

The ingress approach is intended to provide:

- centralized routing
- cleaner exposure model
- easier TLS handling
- consistent service publishing pattern

The platform will evaluate an ingress controller compatible with the Kubernetes and AWS environment and use it as the primary path for exposing user-facing services.

This avoids ad-hoc exposure patterns and improves operational consistency.

---

## Internal Service Exposure
Not every service should be externally reachable.

The platform distinguishes between:

- externally exposed services
- internal-only services
- backend or stateful services

Examples:
- the web application may be exposed through ingress
- worker services should remain internal
- PostgreSQL and Redis should remain non-public

This distinction is important for both security and operational clarity.

---

## Network Isolation Direction
The platform assumes isolation at multiple levels:

- VPC-level boundary
- subnet-level separation
- Kubernetes namespace separation
- future network policy enforcement
- environment separation across `dev`, `stage`, and `prod`

This layered approach helps reduce accidental exposure and creates a stronger baseline for secure multi-environment operation.

---

## Security-Related Networking Assumptions
The networking design supports the following security assumptions:

- internal services should not be exposed publicly by default
- production exposure paths should be explicit and reviewed
- traffic entry points should be limited and understandable
- application communication paths should become more controlled over time
- network policy adoption should be part of the platform evolution

This aligns networking with the broader secure-by-default platform design.

---

## Environment Differences
The same networking model should be reused across environments with controlled differences.

### Development
- lower scale
- lower traffic expectations
- simpler cost profile

### Staging
- production-like topology where practical
- suitable for validating ingress and routing behavior

### Production
- strongest separation expectations
- clearest exposure controls
- highest attention to failure domains and availability

Environment differences should not break the overall design model.

---

## Failure Domain Awareness
Networking design must consider failure domains and blast radius.

Key principles include:

- avoid unnecessary coupling between unrelated services
- keep public entry points controlled
- prefer designs that are easier to troubleshoot
- ensure the traffic path is understandable during incidents

This is especially important for a platform intended to evolve toward SRE and incident-driven operations.

---

## Future Extensions
This networking model is intended to evolve with:

- network policies
- service mesh evaluation
- deeper east-west traffic control
- traffic observability improvements
- progressive delivery routing strategies
- stronger zero-trust style communication patterns

---

## Known Limitations
This document describes a reference networking direction rather than a fully implemented enterprise network architecture.

It demonstrates:
- architectural intent
- secure-by-default direction
- awareness of AWS and Kubernetes networking concerns

It does not yet document every low-level implementation detail such as final subnet CIDR design, exact route table entries, or final controller selection.

---

## Summary
The platform networking model is built around a simple but strong principle: keep public exposure narrow, keep workloads private by default, and treat internal communication as something to be designed deliberately.

This creates a credible foundation for secure application delivery, operational clarity, and future platform evolution.
