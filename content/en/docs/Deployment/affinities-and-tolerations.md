---
title: "Kubernetes Affinities and Tolerations"
linkTitle: "Affinities and Tolerations"
date: 2020-09-04
description: >
  Managing which Kubernetes nodes workloads get deployed to
---

This document describes how the administrator may influence how the various
workloads that are part of KubeCF will get deployed onto their Kuberntes
cluster.

## Affinities

Kubernetes will attempt to schedule work based on their [affinities and
anti-affinities]; by default, KubeCF will request for each instance group to be
scheduled away from other replicas of the same instance group.  Additionally,
by default the `router` instance group and the `diego-cell` instance group will
have an anti-affinity towards each other.

[affinities and anti-affinities]: https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity

The affinities can be overridden on a per-instance-group basis, using helm
configuration values such as the following:

```yaml
sizing:
  diego-cell:
    affinity:
      podAffinity:
        preferredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchExpressions:
            - key: quarks.cloudfoundry.org/pod-ordinal
              operator: In
              values:
              - "0"
```

This will request diego-cell pods to avoid any other pods that have an ordinal
of zero.  Note that if any affinity or anti-affinity options are given, they
will override the default anti-affinities; it is recommended that they be
specified explicitly as well.

## Tolerations

Kubernetes has a concept of [tolerations], which can be used to prevent some
workloads from running on a given [node]; this can be used to do things such as
ensuring the physical host has the appropriate kinds of resources, or to evict
work from nodes that will be removed.

[tolerations]: https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/
[node]: https://kubernetes.io/docs/concepts/architecture/nodes/

This can be configured in KubeCF on an instance group level, by providing the
appropriate configuration in the helm values; as an example, given the
following:

```yaml
sizing:
  nats:
    tolerations:
    - key: node.kubernetes.io/out-of-disk
      operator: Exists
      effect: NoSchedule
```

Then any NATS containers will be allowed to run on nodes that are out of disk.
