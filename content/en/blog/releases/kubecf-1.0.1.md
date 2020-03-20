
---
title: "KubeCF Release v1.0.1"
linkTitle: "KubeCF Release v1.0.1"
date: 2020-03-19
description: >
  KubeCF 1.0.1 has been released!
---

# Bug fixes

- Fixes the permissions issue in autoscaler db when the pod restarts. [#467](https://github.com/cloudfoundry-incubator/kubecf/pull/467)
- Moved pxc image to cfcontainerization organization, like all the other images. [#479](https://github.com/cloudfoundry-incubator/kubecf/pull/479)
- The database-seeder job pod was not being deleted after KubeCF helm installation was removed with success. [#493](https://github.com/cloudfoundry-incubator/kubecf/pull/493)
- Security group setups. [#485](https://github.com/cloudfoundry-incubator/kubecf/pull/485)

# Maintenance

- cf-operator bumped to version 3.3.0+0.gf32b521e.

# Dependencies

| Name            | Version                           | Description                                                                                      |
|---                  |---                                    |---                                                                                                      |
|cf-operator   | 3.3.0+0.gf32b521e      |Processes BOSH deployments. Maps them to kube objects   |

# Quick Fresh Installation

These are the basic guidelines to deploy KubeCF in a development environment. For more detailed information check [here](doc/dev/general.md).

Before starting the deployment phase, make sure that the **values.yaml** file contains all the needed properties with valid values. For more information about the available properties check [here](https://github.com/SUSE/kubecf/blob/002b49ad26109e175812c04a3764bd8712e54580/deploy/helm/kubecf/values.yaml).

Download the release bundle artifact extract the content to a local folder.

## cf-operator

```
helm install cf-operator \
  --namespace cf-operator \
  --set "global.operator.watchNamespace=kubecf" \
cf-operator.tgz
```

## KubeCF

Install KubeCF after setting the needed properties in your `values.yaml`.

```
helm install kubecf \
  --namespace kubecf \
  --values values.yaml \
kubecf.tgz
```

# Download

- Bundle: https://github.com/cloudfoundry-incubator/kubecf/releases/download/v1.0.1/kubecf-bundle-v1.0.1.tgz
- Standalone chart: https://github.com/cloudfoundry-incubator/kubecf/releases/download/v1.0.1/kubecf-v1.0.1.tgz