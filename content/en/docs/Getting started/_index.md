---
title: "Getting Started"
linkTitle: "Getting Started"
weight: 2
description: >
  How to deploy KubeCF
---


## Prerequisites

- A Kubernetes cluster
- Presence of a default storage class (provisioner).
- For use with a diego-based kubecf (default), a node OS with XFS
  support.
      - For GKE, using the option `--image-type UBUNTU` with the
        `gcloud beta container` command selects such an OS.
- Helm 3

## Installation

KubeCF is packaged as an Helm chart. 

Check the [release page](https://github.com/cloudfoundry-incubator/kubecf/releases) for a full list of the official releases.

Nightly builds can be found on the [KubeCF public s3 bucket](https://kubecf.s3.amazonaws.com/index.html).

There are two assets for each release: 
one is the "kubecf-bundle" which contains the KubeCF chart along with the cf-operator version required to deploy it, while the standalone chart containts only the KubeCF chart.

The standalone chart (`kubecf.tgz`) contains a `Metadata.yaml` file which indicates the version of the cf-operator to install for a successfull deployment.

## Try it out!


