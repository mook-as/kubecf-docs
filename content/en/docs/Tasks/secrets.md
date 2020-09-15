
---
title: "Secret Rotation in KubeCF"
linkTitle: "KubeCF and Secrets"
weight: 6
date: 2020-03-19
description: >
  How to handle Secrets in KubeCF
---

__Rotating secrets__ is in general the process of updating one or more
secrets to new values and restarting all affected pods so that they
will use these new values.

Most of the process is automatic. How to trigger it is explained
in the following document.

Beyond this, the keys used to encrypt the Cloud Controller Database
(CCDB) can also be rotated, however, they do not exist as general
secrets of the KubeCF deployment. This means that the general process
explained above __does not apply__ to them.  Instead, please refer to
the [dedicated section] for it.

[dedicated section]: {{<ref "#rotating-the-ccdb-encryption-keys">}}

This document applies to operators deploying KubeCF.

### Background

One of the features KubeCF (or rather the quarks-operator it sits on top
of) provides is the ability to declare secrets (passwords and
certificates) and have the system automatically generate something
suitably random for such on deployment, and distribute the results to
the pods using them.

This removes the burden from human operators to come up with lots of
such just to have all the internal components of KubeCF properly wired
up for secure communication.

However, even with this, operators may wish to change such secrets
from time to time, or on a schedule. In other words, re-randomize the
board, and limit the lifetime of any particular secret.

As a note on terminology, this kind of change is called
__rotating a secret__.

This document describes how this can be done, in the context of KubeCF.

### Finding secrets

Retrieve the list of all secrets maintained by a KubeCF deployment (which we
assume here to be deployed to the `kubecf` namespace) via:

    kubectl get QuarksSecret --namespace kubecf

To see the information about a specific secret, for example the NATS password,
use:

    kubectl get QuarksSecret --namespace kubecf var-nats-password --output yaml

Note that each QuarksSecret has a corresponding regular Kubernetes secret it
controls.

    kubectl get secret --namespace kubecf
    kubectl get secret --namespace kubecf var-nats-password --output yaml

### Requesting a rotation for a specific secret

We keep using `var-nats-password` as our example secret.

To rotate this secret:

  1. Create a YAML file for a ConfigMap of the form:

        ---
        apiVersion: v1
        kind: ConfigMap
        metadata:
          name: rotate.var-nats-password
          labels:
            quarks.cloudfoundry.org/secret-rotation: "true"
        data:
          secrets: '["var-nats-password"]'

     Note, while the name of this ConfigMap can be technically
     anything (allowed by Kubernetes syntax) we recommend using a name
     derived from the name of the secret itself, to make the
     connection clear.

     Note further that while this example rotates only a single
     secret, the `data.secrets` key accepts an array of secret names,
     allowing the simultaneous rotation of many secrets together.

  2. Apply this ConfigMap using:

         kubectl apply --namespace kubecf -f /path/to/your/yaml/file

  3. The quarks-operator will process this ConfigMap due the label

         quarks.cloudfoundry.org/secret-rotation: "true"

     and know that it has to invoke a rotation of the referenced
     secrets.

     The actions of the quarks-operator can be followed in its log.

  4. After the quarks-operator has done the rotation, i.e. has not only
     changed the secrets, but also restarted all affected pods (the
     users of the rotated secrets), delete the trigger config map
     again:

        kubectl delete --namespace kubecf -f /path/to/your/yaml/file

## Rotating the CCDB encryption keys

**IMPORTANT** - Always backup the database before rotating the encryption key.

The key used to encrypt the database is generated the first time kubecf is deployed.
It is based on the Helm values:

```yaml
ccdb:
  encryption:
    rotation:
      key_labels:
      - encryption_key_0
      current_key_label: encryption_key_0
```

For each label under `key_labels`, kubecf will generate an encryption key.
The `current_key_label` indicates which key is currently being used.

In order to rotate the CCDB encryption key, add a new label to `key_labels` (keeping the old
labels), and mark the `current_key_label` with the newly added label. Example:

```yaml
ccdb:
  encryption:
    rotation:
      key_labels:
      - encryption_key_0
      - encryption_key_1
      current_key_label: encryption_key_1
```

**IMPORTANT** - key labels should be less than 240 characters long.

Then, update the kubecf Helm installation. After Helm finishes its updates, trigger the
`rotate-cc-database-key` errand:

**Note** - the following command assumes the Helm installation is named `kubecf` and it was
installed to the `kubecf` namespace. These values may be different depending on how kubecf was
installed.

```sh
kubectl patch qjob kubecf-rotate-cc-database-key \
  --namespace kubecf \
  --type merge \
  --patch '{"spec":{"trigger":{"strategy":"now"}}}'
```
