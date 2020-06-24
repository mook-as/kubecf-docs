---
title: "Run smoke tests"
date: 2020-06-24
weight: 5
description: >
  Validate your KubeCF deployment with smoke tests
---

Once you have deployed KubeCF, you might want to validate it by running Cloud
Foundry's smoke tests.
For that purpose, KubeCF's helm chart ships the Cloud Foundry smoke tests,
packaged inside of a deployment's instance group.

## Triggering the smoke tests

The smoke tests are run by a CF-Operator qjob (a wrapper on Kube jobs). That job
is defined to not trigger automatically by default. To start it, patch the job
with trigger strategy "now":

    $ kubectl get qjob --namespace scf --output name 2> /dev/null | grep smoke-tests
    quarksjob.quarks.cloudfoundry.org/smoke-tests

    $ kubectl patch quarksjob.quarks.cloudfoundry.org/smoke-tests \
             --namespace kubecf --type merge --patch \
             '{ "spec": { "trigger": { "strategy": "now" } } }'

This will start the job, which creates a `smoke-tests-<id>` pod. Inside that
pod, there's a container called `smoke-tests-smoke-tests` with the test run.

You can, as usual, see the resulting logs from the smoke-tests pod with:

    $ kubectl logs -f smoke-tests-614496c133797980-bm2g4 --namespace scf \
              --container smoke-tests-smoke-tests
    Running smoke tests...
    Running binaries smoke/isolation_segments/isolation_segments.test
    smoke/logging/logging.test
    smoke/runtime/runtime.test
    [1592406628] CF-Isolation-Segment-Smoke-Tests - 4 specs - 7 nodes SSSS SUCCESS! 13.717155722s
    [1592406628] CF-Logging-Smoke-Tests - 2 specs - 7 nodes S• SUCCESS! 36.291956367s
    [1592406628] CF-Runtime-Smoke-Tests - 2 specs - 7 nodes S• SUCCESS! 30.456607562s

    Ginkgo ran 3 suites in 1m21.517660359s
    Test Suite Passed

The pod will exit with a return code of `0` if successful, and other if not.
