---
title: "Getting Started"
linkTitle: "Getting Started"
weight: 2
description: >
  How to deploy KubeCF
---

{{% pageinfo %}}
There is not an official release yet, [check out our nightlies](https://kubecf.s3.amazonaws.com/index.html).
{{% /pageinfo %}}

## Prerequisites

- A Kubernetes cluster
- Presence of a default storage class (provisioner).
- For use with a diego-based kubecf (default), a node OS with XFS
  support.
      - For GKE, using the option `--image-type UBUNTU` with the
        `gcloud beta container` command selects such an OS.

## Installation

KubeCF is packaged as an Helm chart. 

Currently there isn't any official release. 

Nightly builds can be found on the [KubeCF public s3 bucket](https://kubecf.s3.amazonaws.com/index.html).

## Try it out!


