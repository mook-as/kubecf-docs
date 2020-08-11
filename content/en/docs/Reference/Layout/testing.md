---
title: "Testing"
linkTitle: "Testing"
date: 2017-01-05
description: >
  Bazel targets to run CF smoke and acceptance tests.
---

The smoke, brain, acceptance, and sync integration tests can be run after KubeCF
deployment has completed, via:

```sh
make tests
```

or each by:

```sh
make smoke
make brain
make sits
make cats
```

The [acceptance tests] can be limited to specific suites of interest,
as explained in the linked document.

[acceptance tests]: tests_cat.md
