---
title: "Project Layout"
linkTitle: "Project Layout"
date: 2017-01-05
description: >
  What are the files in the KubeCF project?
---

The important directories of the KubeCF sources, and their contents
are shown in the table below. Each directory entry links to the
associated documentation, if we have any.

|Directory                                                              |Content                                                |
|---                                                                    |---                                                    |
|__top__                                                                |Documentation entrypoint, License,                     |
|                                                                       |Main workspace definitions.                            |
|__top__/.../README.md                                                  |Directory-specific local documentation.                |
|[__top__/bosh/releases](github.com/cloudfoundry-incubator/kubecf/blob/master/bosh/releases/pre_render_scripts/README.md) |Support for runtime patches of a kubecf deployment.    |
|__top__/doc                                                            |Global documentation.                                  |
|[__top__/dev/cf_deployment/bump](github.com/cloudfoundry-incubator/kubecf/blob/master/doc/cf_deployment/bump.md)                |Tools to support updating the cf deployment            |
|                                                                       |manifest used by kubecf.                               |
|[__top__/dev/cf_cli](github.com/cloudfoundry-incubator/kubecf/blob/master/doc/cf_cli.md)                                        |Deploy cf cli into a helper pod from which to then     |
|                                                                       |inspect the deployed Kubecf                            |
|[__top__/dev/kube](github.com/cloudfoundry-incubator/kubecf/blob/master/doc/inspection.md)                                      |Tools to inspect kube clusters and kubecf deployments. |
|[__top__/dev/kubecf](github.com/cloudfoundry-incubator/kubecf/blob/master/dev/kubecf/README.md)                          |Kubecf chart configuration                             |
|__top__/deploy/helm/kubecf                                             |Templates and assets wrapping a CF deployment          |
|                                                                       |manifest into a helm chart.                            |
|__top__/rules                                                          |Supporting scripts.                                    |
|[__top__/testing](github.com/cloudfoundry-incubator/kubecf/blob/master/doc/tests.md)                                            |Scripts with specific testing                          |
|[__top__/scripts](github.com/cloudfoundry-incubator/kubecf/blob/master/scripts/README.md)                                |Developer scripts used by make to start a k8s cluster  |
|                                                                       |(for example on kind), lint, build, run & test kubecf  |
|[__top__/scripts/tools](github.com/cloudfoundry-incubator/kubecf/blob/master/scripts/tools/README.md)                    |Developer scripts pinning the development dependencies |
