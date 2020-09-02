---
title: "Eirini Persistence broker"
date: 2020-09-02
weight: 4
description: >
  How to set-up KubeCF with Eirini and the persistence broker
---

KubeCF allows to leverage the Kubernetes Cluster available `StorageClass`. When Eirini is being enabled, two additional components are deployed, the `eirini-persi-extension` and `eirini-persi-broker`.

The broker must be configured in order to use the storageclass to provide support for persistence data for applications pushed to CloudFoundry with Eirini.

You can set up default plans that are applied to the broker during deployment. 

To configure the broker, in the KubeCF values add and adjust as needed:

```yaml
eirinix:
  persi-broker:
    service-plans:
    - id: default
      name: "default"
      description: "Existing default storage class"
      kube_storage_class: "default" # Storageclass used for provisioning
      free: true
      default_size: "1Gi" # Default size of generated PVC
      default_access_mode: "ReadWriteOnly" # Here you can tweak the default access mode for new PVCs
```

## Setup eirini-persi-broker with KubeCF

In this section we will see how to setup the eirini-persi-broker on a KubeCF deployment.

### Get the broker password

After deploying KubeCF the broker password should be automatically generated (the example assumes you have deployed KubeCF in the `kubecf` namespace):

```
$> BROKER_PASS=$(kubectl get secrets -n kubecf -o json persi-broker-auth-password | jq -r '.data."password"' | base64 -d)
```

### Create the service broker

With `cf-cli` (you must be logged in) let's add the broker to our cf instance:
```
$> cf create-service-broker eirini-persi admin $BROKER_PASS http://eirini-persi-broker:8999
```

### Enable the service for the space

Let's enable the service (here, doing it globally):

```
$> cf enable-service-access eirini-persi


Enabling access to all plans of service eirini-persi for all orgs as admin...
OK

```

Check everything is ok:

```bash
$> cf service-brokers

Getting service brokers as admin...

name                  url
eirini-persi          http://eirini-persi-broker:8999
```

If eirini-persi is showed among the list of the available brokers, it means that it is configured and plans supplied during deployment are available to be consumed.


Now the broker should be available in the marketplace, list all the broker services:

```
$> cf marketplace
Getting services from marketplace in org system / space tmp as admin...
OK

service        plans     description
eirini-persi   default   Eirini persistence broker
```


List the plans available from the broker (output might differ):
```
$> cf marketplace -s eirini-persi
Getting service plan information for service eirini-persi as admin...
OK

service plan   description                 free or paid
default        Eirini persistence broker   free
```



## Using the Eirini-persi broker

To verify that everything works correctly it is possible to check after creating a service that the persistent volume claims are present in the Kubernetes cluster.

### Create a service

`eirini-persi-broker` will create PVCs associated to the services that are created:

```
$> cf create-service eirini-persi default eirini-persi-1
Creating service instance eirini-persi-1 in org system / space tmp as admin...
OK
```

In this way, we can associate the service to an app, which will actually link and attach a PVC to it.

List all the ```PersistentVolumeClaim``` and verify that a new one was created:

```
$> kubectl get pvc -n eirini
NAME                                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
f9d4b977-0b9e-4fcb-a92e-652bc77dd7e7   Bound    pvc-9960cc11-7895-11e9-afac-024267c84a6b   1Gi        RWO            persistent     6s

```

To create a service with a custom capacity, you can define the quota when creating the service.

E.g. create a service providing a volume with 20M of quota:

```
$> cf create-service eirini-persi default eirini-persi-1-20M-quota -c '{"size": "20M"}'
```

E.g. To create a PVC which has a different access mode:


```
$> cf create-service eirini-persi default eirini-persi-1-rwm -c '{"access_mode": "ReadWriteMany"}'
```


### Bind volumes to a Cloud Foundry application

List the available services:

```
$> cf services
Getting services in org system / space tmp as admin...

name                          service        plan      bound apps    last operation
eirini-persi-1                eirini-persi   default   dizzylizard   create succeeded
```

Let's associate the service to an application (in this case ```dizzylizard```) using ```eirini-persi-1```:

```
$> cf bind-service dizzylizard eirini-persi-1

Binding service eirini-persi-1 to app dizzylizard in org system / space tmp as admin...
OK

```

Restage our app so our change takes effect:
```
$> cf restage dizzylizard

Restaging app dizzylizard in org system / space tmp as admin...
```


### Access the Volume

Once the service is associated, the application can access to the data of the volume mount by reading the mounted path inside the ```VCAP_SERVICES``` environment variable.

An example of ```VCAP_SERVICES``` generated by ```eirini-persi-broker```:

```json
{"eirini-persi": [	  {
		"credentials": { "volume_id": "the-volume-id" },
		"label": "eirini-persi",
		"name": "my-instance",
		"plan": "hostpath",
		"tags": [
			"erini",
			"kubernetes",
			"storage"
		],
		"volume_mounts": [
		  {
			"container_dir": "/var/vcap/data/de847d34-bdcc-4c5d-92b1-cf2158a15b47",
			"device_type": "shared",
			"mode": "rw"
		  }
		]
	  }
	]
}
```

You can refer to the [Cloud Foundry documentation regarding of how to access to the Volume Service](https://docs.cloudfoundry.org/devguide/services/using-vol-services.html), with the difference that the eirini broker will create services with the id ```eirini-persi```.


## See also

- [https://github.com/SUSE/scf/wiki/Persistence-with-Eirini-in-SCF](https://github.com/SUSE/scf/wiki/Persistence-with-Eirini-in-SCF)