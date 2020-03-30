
---
title: "Deploy KubeCF"
linkTitle: "KubeCF Deployment guidelines"
weight: 6
date: 2020-03-26
description: >
  Few heads up while deploying KubeCF
---

### Deploying on RHEL / CentOS 7 -based Kubernetes

If you are deploying with diego (that is, Eirini is not enabled) on top of a
RHEL / CentOS 7 based cluster, please make sure that the
`user.max_user_namespaces` `sysctl` is set to a large number.  Do this on any
worker node that may host any `diego-cell` workloads:

```bash
sudo sh -c 'sysctl -w user.max_user_namespaces=15076 | tee -a /etc/sysctl.conf'
```

This is not necessary on RHEL / CentOS 8 based systems.
