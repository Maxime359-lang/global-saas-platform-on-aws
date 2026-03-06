# Storage Architecture

## Purpose
This document describes the storage model of the reference AWS and Kubernetes platform.

Its goal is to explain how the platform treats stateless and stateful workloads, how persistent data is handled, and how storage decisions support reliability, operability, and future platform evolution.

This is a reference storage architecture for a portfolio platform. It is intended to demonstrate sound platform engineering decisions rather than replicate every detail of a production storage estate.

---

## Storage Goals
The storage model is designed to achieve the following goals:

- clearly distinguish between stateless and stateful workloads
- provide persistent storage where data durability is required
- align storage design with Kubernetes and AWS operating patterns
- support secure and maintainable handling of application state
- provide a path toward backup, restore, and disaster recovery practices
- demonstrate awareness of CSI-related design decisions

---

## Workload Storage Categories
The platform distinguishes between two major workload categories:

### Stateless workloads
Examples:
- web application containers
- API services
- selected background workers

Characteristics:
- do not require persistent local storage for correct operation
- can be replaced or rescheduled without preserving container-local state
- should rely on external systems for durable state

This model improves scalability and reduces operational complexity.

### Stateful workloads
Examples:
- PostgreSQL
- selected queue or cache scenarios where persistence matters
- platform components that require durable configuration or data

Characteristics:
- require persistent storage across restarts or rescheduling
- need stronger operational safeguards
- must be considered in backup and restore planning

---

## Reference Stateful Components
The reference platform includes two major state-related backend components:

### PostgreSQL
PostgreSQL is treated as the primary system of record for application data.

Storage expectations:
- durable storage
- predictable persistence across pod or node changes
- backup and restore awareness
- careful handling of data integrity and recovery operations

### Redis
Redis is used as a cache and queue support component.

Storage expectations depend on role:
- if used purely as cache, persistence requirements may be lower
- if used for queue durability or operational state, persistence considerations become more important

This distinction matters because not every stateful-looking service requires the same durability guarantees.

---

## Kubernetes Storage Direction
The platform is intended to use Kubernetes-native storage concepts for persistent data handling.

Core concepts include:

- PersistentVolumeClaim (PVC)
- PersistentVolume (PV)
- StorageClass
- dynamic provisioning
- workload attachment to persistent storage

This model provides a clean separation between workload intent and storage implementation details.

---

## CSI Considerations
Because the target role expects deep Kubernetes understanding, the storage architecture must explicitly acknowledge Container Storage Interface (CSI) concerns.

The platform direction assumes:
- storage provisioning should be treated as a platform decision
- workloads should request storage through Kubernetes abstractions
- the underlying AWS-backed implementation should remain consistent with platform standards

Areas to consider include:
- dynamic volume provisioning
- storage class selection
- reclaim policy
- performance characteristics
- backup compatibility
- operational simplicity

A final implementation direction will be supported by dedicated architecture decisions and infrastructure structure.

---

## AWS Storage Direction
Within AWS, the reference platform is expected to evaluate storage options appropriate for Kubernetes-based workloads.

### Block storage direction
For database-like workloads, block storage is the primary expected direction.

This aligns well with:
- database persistence
- predictable attachment patterns
- performance-focused persistent workloads

### Shared filesystem direction
Some use cases may benefit from shared storage semantics.

This may be relevant for:
- shared assets
- selected platform tooling
- future extensions where multiple workloads need access to the same file space

The platform may document such an option, but it should not be treated as the default for database persistence.

---

## Storage Classes
Storage classes should be used to standardize how workloads request persistent storage.

Their purpose is to:
- define provisioning behavior
- reflect platform-approved storage options
- separate application intent from infrastructure detail
- make storage usage more consistent across environments

The portfolio platform should show that storage is not attached ad hoc, but requested through intentional platform primitives.

---

## Dynamic Provisioning
Dynamic provisioning is the preferred direction for the platform.

Benefits:
- reduces manual storage handling
- improves repeatability
- aligns with Kubernetes operating patterns
- makes environment management cleaner

This allows workloads to request storage in a declarative way rather than relying on manually prepared volumes.

---

## Persistence Strategy by Component

### Web application
Expected model:
- stateless
- no durable local data dependency
- externalize persistent state to PostgreSQL and Redis

### Background worker
Expected model:
- mostly stateless
- uses backend systems for durable data
- should not rely on container-local persistence

### PostgreSQL
Expected model:
- persistent volume required
- careful storage handling
- backup and restore consideration mandatory

### Redis
Expected model:
- persistence strategy depends on functional role
- if cache-only, durability requirements are lower
- if queue semantics matter, persistence discussion becomes more important

---

## Operational Storage Concerns
Storage design must support not just initial deployment, but also ongoing operations.

Important operational concerns include:

- what data must survive restarts
- how persistent data is restored
- how storage affects recovery time
- what happens when a node fails
- how storage choices affect troubleshooting
- how environment differences influence cost and resilience

This is especially important for platform work, because stateful services increase operational risk compared with stateless workloads.

---

## Backup and Restore Direction
A production-oriented platform must treat backup and restore as a first-class concern.

The platform direction assumes:
- PostgreSQL requires a backup strategy
- restore procedures must be documented and testable
- storage decisions should support recovery, not just persistence
- backup thinking should be connected to RPO and RTO goals in future documentation

At this stage, the storage architecture establishes backup and restore as mandatory design concerns even if full implementation comes later.

---

## Reclaim and Lifecycle Awareness
Persistent storage must be treated carefully across workload lifecycle events.

Important concerns include:

- what happens to volumes after workload deletion
- whether data should be preserved or removed
- how non-production cleanup differs from production protection
- how accidental data loss is prevented

This should be formalized later through storage policy and environment rules.

---

## Environment Differences

### Development
- lower scale
- lower durability expectations for non-critical data
- cost-sensitive setup
- may use simplified persistence strategy where appropriate

### Staging
- closer to production persistence behavior
- suitable for validating backup and restore assumptions
- should reflect realistic workload/storage relationships

### Production
- strongest durability expectations
- strictest handling of stateful services
- highest requirement for storage reliability and data protection

Environment differences should be intentional and documented.

---

## Security Considerations for Storage
Storage must also be viewed through a security lens.

Relevant concerns include:
- access boundaries for stateful services
- protection of sensitive application data
- separation of environments
- secret handling for database access
- avoiding unnecessary exposure of storage-backed services

Storage decisions should not be treated purely as capacity decisions; they also affect platform security posture.

---

## Cost Considerations
Storage design affects cost directly.

Relevant cost areas include:
- persistent volume allocation
- storage class selection
- backup retention
- recovery-related storage overhead
- different persistence needs across environments

The architecture should balance simplicity, resilience, and cost awareness.

---

## Future Extensions
This storage model is intended to evolve with:

- more explicit CSI decisions
- final storage class definitions
- backup implementation details
- restore testing
- RPO/RTO documentation
- database high availability strategy
- deeper Redis persistence analysis

---

## Known Limitations
This document describes a reference storage direction rather than a full production data platform.

It demonstrates:
- awareness of stateless vs stateful boundaries
- Kubernetes storage design thinking
- AWS-aligned storage direction
- backup and recovery awareness

It does not yet define full implementation details such as exact storage classes, benchmark-driven IOPS tuning, or final database HA topology.

---

## Summary
The platform storage model is built on a simple principle: keep application workloads stateless where possible, treat persistent data as a deliberate platform concern, and design storage in a way that supports durability, recovery, and operational clarity.

This creates a credible foundation for Kubernetes-based state handling and future platform maturity.
