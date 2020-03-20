
---
title: "KubeCF Release v1.0"
linkTitle: "KubeCF Release v1.0"
date: 2020-03-19
description: >
  KubeCF 1.0 has been released!
---
KubeCF first major release is out 🥳 ...

![kubecf-1.0](https://media.giphy.com/media/Sk5uipPXyBjfW/giphy.gif "KubeCF 1.0!")

# Features

- HA database configuration for CCDB and UAA based on the [upstream PXC chart](https://github.com/helm/charts/tree/master/stable/percona-xtradb-cluster)
 [#312](https://github.com/cloudfoundry-incubator/kubecf/issues/312)
- Stratos on KubeCF [#199](https://github.com/cloudfoundry-incubator/kubecf/issues/199)
- Bump all releases to use SLE15 base image [#373](https://github.com/cloudfoundry-incubator/kubecf/issues/373)
- Add SITS to CI [#423](https://github.com/cloudfoundry-incubator/kubecf/issues/423)
- Bump cf-deployment to 12.33 [#413](https://github.com/cloudfoundry-incubator/kubecf/pull/413)
- Bump cf-operator to 3.2.1 [#446](https://github.com/cloudfoundry-incubator/kubecf/pull/446)
- [Public Concourse CI](https://concourse.suse.dev/teams/main/pipelines/kubecf)

You can find all the closed no-bug issues [here](https://github.com/SUSE/kubecf/issues?utf8=%E2%9C%93&q=is%3Aclosed+milestone%3A1.0.0+-label%3A%22Type%3A+Bug%22+-label%3A%22Status%3A+Not+Implemented%22+).

# Bug fixes

- fix: allow only credhub and uaa for apps [#434](https://github.com/cloudfoundry-incubator/kubecf/pull/434)
- fix: helm: reduce duplication in sizing ops [#440](https://github.com/cloudfoundry-incubator/kubecf/pull/440)
- fix: allow only credhub and uaa for apps [#434](https://github.com/cloudfoundry-incubator/kubecf/pull/434)
- Fixing out of memory issue when scaling doppler instances [#366](https://github.com/cloudfoundry-incubator/kubecf/pull/366)
- Remove security group that allows apps to communicate with internal network [#304](https://github.com/cloudfoundry-incubator/kubecf/issues/304)

You can find all the fixed bugs [here](https://github.com/SUSE/kubecf/issues?utf8=%E2%9C%93&q=is%3Aclosed+milestone%3A1.0.0+label%3A%22Type%3A+Bug%22+-label%3A%22Status%3A+Not+Implemented%22+).

# Dependencies

| Name            | Version                           | Description                                                                                      |
|---                  |---                                    |---                                                                                                      |
|cf-operator   |3.2.1-.ga32a3f79       |Processes BOSH deployments. Maps them to kube objects   |

# Quick Fresh Installation

These are the basic guidelines to deploy KubeCF in a development environment. For more detailed information check [here](doc/dev/general.md).

Before starting the deployment phase, make sure that the **values.yaml** file contains all the needed properties with valid values. For more information about the available properties check [here](https://github.com/SUSE/kubecf/blob/002b49ad26109e175812c04a3764bd8712e54580/deploy/helm/kubecf/values.yaml).

Download the release bundle artifact extract the content to a local folder.

## cf-operator

```
helm install cf-operator \
  --namespace cf-operator \
  --set "global.operator.watchNamespace=kubecf" \
cf-operator-3.2.1+0.ga32a3f79.tgz
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

- Bundle: https://github.com/cloudfoundry-incubator/kubecf/releases/download/v1.0.0/kubecf-bundle-v1.0.0.tgz
- Standalone chart: https://github.com/cloudfoundry-incubator/kubecf/releases/download/v1.0.0/kubecf-v1.0.0.tgz