---
kep-number: TBD
title: Runtime Class
authors:
  - "@tallclair"
owning-sig: sig-node
participating-sigs:
reviewers:
  - TBD
approvers:
  - TBD
editor: TBD
creation-date: yyyy-mm-dd
last-updated: yyyy-mm-dd
status: provisional
---

# Runtime Class

## Table of Contents

FIXME:

* [Table of Contents](#table-of-contents)
* [Summary](#summary)
* [Motivation](#motivation)
    * [Goals](#goals)
    * [Non-Goals](#non-goals)
* [Proposal](#proposal)
    * [User Stories [optional]](#user-stories-optional)
      * [Story 1](#story-1)
      * [Story 2](#story-2)
    * [Implementation Details/Notes/Constraints [optional]](#implementation-detailsnotesconstraints-optional)
    * [Risks and Mitigations](#risks-and-mitigations)
* [Graduation Criteria](#graduation-criteria)
* [Implementation History](#implementation-history)
* [Drawbacks [optional]](#drawbacks-optional)
* [Alternatives [optional]](#alternatives-optional)

## Summary

TODO

## Motivation

TODO

### Goals

- Provide a mechanism for surfacing container runtime properties to the control plane
- Support multiple runtimes per-cluster, and provide a mechanism for users to select the desired
  runtime

### Non-Goals

- RuntimeClass is NOT a general policy mechanism
- RuntimeClass is NOT "NodeClass". Although different nodes may run different runtimes, in general
  RuntimeClass should not be a cross product of runtime properties and node properties.

The following goals are out-of-scope for the initial implementation, but may be explored in a future
iteration:

- Automatic runtime or feature discovery - initially RuntimeClasses are manually defined (by the
  cluster admin or provider), and are asserted to be an accurate representation of the runtime.
- Scheduling in heterogeneous clusters - it is possible to operate a heterogeneous cluster
  (different runtime configurations on different nodes) through scheduling primitives like
  `NodeAffinity` and `Taints+Tolerations`, but the user is responsible for setting these up and
  automatic runtime-aware scheduling is out-of-scope.
- FIXME: Define standardized or conformant runtime classes - although we would like to declare some
  predefined RuntimeClasses with specific properties, doing so is out-of-scope for this initial KEP.
- [Pod Overhead][] - Although RuntimeClass is likely to be the configuration mechanism of choice,
  the details of how pod resource overhead will be implemented is out of scope for this KEP.

[Pod Overhead]: https://docs.google.com/document/d/1EJKT4gyl58-kzt2bnwkv08MIUZ6lkDpXcxkHqCvvAp4/edit

## Proposal

### User Stories

- As a cluster operator, I want to provide multiple runtime options to support a wide variety of
  workloads. Examples include native linux containers, "sandboxed" containers, and windows
  containers.
- As a cluster operator, I want to provide stable rolling upgrades of nodes.
- As an application developer, I want to select the runtime that best fits my workload.
- As an application developer, I don't want to study the nitty-gritty details of different runtime
  implementations.
- As an application developer, I want my application to be portable across clusters that use similar
  but different variants of a "class" of runtimes.
- As an application operator, I want to know precisely how the application is running, and which
  features are being used.
- As an application operator, I want pods to fail fast when trying to use an optional feature that
  isn't supported by the chosen runtime.
- As a security operator, I want to ensure that pods are running with the security features they
  claim.

### Examples of runtime variation

- Linux Security Module (LSM) choice - Kubernetes supports both AppArmor & SELinux options on pods,
  but those are mutually exclusive, and support of either is not required by the runtime. The
  default configuration is also not well defined.
- Seccomp-bpf - Kubernetes has alpha support for specifying a seccomp profile, but the default is
  defined by the runtime, and support is not guaranteed.
- Windows containers - isolation features are very OS-specific, and most of the current features are
  limited to linux. As we build out Windows container support, we'll need to add windows-specific
  features as well.
- Host namespaces (Network,PID,IPC) may not be supported by virtualization-based runtimes
  (e.g. Kata-containers & visor).
- Per-pod and Per-container resource overhead varies by runtime.
- Device support (e.g. GPUs) varies wildly by runtime & nodes.
- Supported volume types varies by node - it remains TBD whether this information belongs in
  RuntimeClass.
- The list of default capabilities is defined in Docker, but not Kubernetes. Future runtimes may
  have differing defaults, or support a subset of capabilities.
- `Privileged` mode is not well defined, and thus may have differing implementations.

### Implementation Details/Notes/Constraints [optional]

What are the caveats to the implementation?
What are some important details that didn't come across above.
Go in to as much detail as necessary here.
This might be a good place to talk about core concepts and how they releate.

### Risks and Mitigations

What are the risks of this proposal and how do we mitigate.
Think broadly.
For example, consider both security and how this will impact the larger kubernetes ecosystem.

## Graduation Criteria

How will we know that this has succeeded?
Gathering user feedback is crucial for building high quality experiences and SIGs have the important responsibility of setting milestones for stability and completeness.
Hopefully the content previously contained in [umbrella issues][] will be tracked in the `Graduation Criteria` section.

[umbrella issues]: https://github.com/kubernetes/kubernetes/issues/42752

## Implementation History

FIXME

- 2018-06-11: Initial proposal

## Drawbacks [optional]

Why should this KEP _not_ be implemented.

## Alternatives [optional]

Similar to the `Drawbacks` section the `Alternatives` section is used to highlight and record other possible approaches to delivering the value proposed by a KEP.
