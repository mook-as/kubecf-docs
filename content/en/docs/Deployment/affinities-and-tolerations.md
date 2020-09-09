---
title: "Kubernetes Affinities and Tolerations"
linkTitle: "Affinities and Tolerations"
weight: 50
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
  uaa:
    affinity:
      podAntiAffinity:
        preferredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchExpressions:
            - key: quarks.cloudfoundry.org/quarks-statefulset-name
              operator: In
              values:
              - uaa
              - api
```

This will request `uaa` pods to avoid any other `uaa` or `api` pods; this may be
helpful if your UAA instances consume resources to the point where they slow
down API access.  Note that if any affinity or anti-affinity options are given,
they will override the default anti-affinities; it is recommended that they be
specified explicitly as well, as given in the example above.

Note that it is also possible to declare `nodeAffinity` and `podAffinity`, as
the whole affinity block is assumed to be a valid Kubernetes [affinity block].

[affinity block]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#affinity-v1-core

## Tolerations

Kubernetes has a concept of [taints and tolerations], which can be used to
prevent workloads from running on a given [node], and then whitelist some
workloads on it again; this can be used to do things such as ensuring the
physical host has the appropriate kinds of resources, or to evict work from
nodes that will be removed.

[taints and tolerations]: https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/
[node]: https://kubernetes.io/docs/concepts/architecture/nodes/

Tolerations can be configured in KubeCF on an instance group level, by providing
the appropriate configuration in the helm values.  For example, to allocate a
Kubernetes node such that it will only run digeo-cell, we could do:

```bash
# This marks the node "beefy-node" with a taint of "instance-group"
# with a value of "diego-cell", and prevents scheduling workloads that
# do not have a matching taint.
kubectl taint nodes beefy-node instance-group=diego-cell:NoSchedule
```

We could then allow diego-cell to be scheduled onto that node by deploying
KubeCF with a helm `values.yaml` that contains the toleration:

```yaml
sizing:
  diego-cell:
    tolerations:
    - key: instance-group
      operator: Equal
      value: diego-cell
      effect: NoSchedule
```

Then any diego-cell containers will be allowed to run on the `beefy-node` node.
This, of course, does not guarantee that those containers will actually run on
that node; it is also available to run on any other node.  In order to enforce
placement, we will also need to add a node label:

```bash
kubectl label nodes beefy-node instance-group-label=diego-cell
```

It is then possible to add a node affinity term to the instane group, so that
it will always be scheduled on nodes with the given label:

```yaml
sizing:
  diego-cell:
    # tolerations: as above
    affinity:
      nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
          nodeSelectorTerms:
          - matchExpressions:
            - key: instance-group-label
              operator: In
              values:
              - diego-cell
      # podAntiAffinity term, as above; the defaults will be lost.
```
