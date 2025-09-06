# RHOSP 18 Deployment Guide

<!--
__Prerequisites__

- RHOSP 18 installed
- Alletra MP reachable
- Required packages installed

__Deployment Steps__

The details steps are available in this [GitHub page](https://github.com/hpe-storage/hpe-openstack-docs/tree/main/hpe-alletra-mp-rhoso18)
-->

__Overview__

This guide shows how to configure and deploy the HPE Cinder driver in a __Red Hat OpenStack Services on OpenShift (RHOSO) 18.0__ deployment. After reading this, you’ll be able to define the proper configuration and deploy single or multiple HPE Cinder back ends in a RHOSO cluster.

For more information about RHOSO, please refer to its [documentation pages](https://docs.redhat.com/en/documentation/red_hat_openstack_services_on_openshift/18.0/html/deploying_red_hat_openstack_services_on_openshift/index).

In Red Hat OpenStack Services on OpenShift 18.0, the HPE cinder volume drivers support the following dataplanes:
* iSCSI
* FibreChannel [certification pending]

__Prerequisites__

* Array FW:
  - HPE 3PAR array 3.3.1 or higher
  - HPE Primera array 4.2 or higher
  - HPE Alletra 9000 array 9.4 or higher
  - HPE Alletra MP B10000 array 10.4 or higher
* Deploy the RHOSO control plane on an OpenShift cluster configured with three master nodes and three worker nodes.
* RHOSO control plane where Cinder services will be installed.
* Ensure network connectivity between the storage backend, the Red Hat OpenShift cluster, and the Compute nodes.

__Deployment Steps__

__Prepare the OpenStack Control Plane CR__

Install the OpenStack Operator for Red Hat OpenStack Services on OpenShift (RHOSO), create a RHOSO control plane on a Red Hat OpenShift Container Platform cluster, and use the OpenStack Operator to deploy a RHOSO data plane.

__Use Certified HPE Cinder Volume Image__

Red Hat requires that you utilize the Certified HPE Cinder Volume Image when deploying RHOSO18 with a HPE Storage array backend.

This container can be found in [https://catalog.redhat.com/software/containers/hpe3parcinder/openstack-cinder-volume-hpe3parcinder18-0/67f4da13dd71f939eb652d57?gs&q=HPE](https://catalog.redhat.com/software/containers/hpe3parcinder/openstack-cinder-volume-hpe3parcinder18-0/67f4da13dd71f939eb652d57?gs&q=HPE).

Ensure the certified image is added to the ```openstackversion``` CR. This is defined in the following YAML file (```openstack_version.yaml```):

```
apiVersion: core.openstack.org/v1beta1
kind: OpenStackVersion
metadata:
  name: openstack
spec:
  customContainerImages:
    cinderVolumeImages:
      volume1: registry.connect.redhat.com/hpe3parcinder/openstack-cinder-volume-hpe3parcinder18-0:18.0
```

Save this file and update:

```
$ oc apply -f openstack-version.yaml
```

__Create a Secret file__

It is necessary to create a secret file that will contain the access credential(s) for your backend HPE Storage Array(s) in your RHOSO deployment.

In this following example file (```cinder-volume-alletra-iscsi-secret.yaml```) secrets are provided for one backend HPE Alletra Storage Array. You need to define a unique secret for each of your backends in case of multiple backend configuration.

```
apiVersion: v1
kind: Secret
metadata:
  labels:
    service: cinder
    component: cinder-volume
  name: cinder-volume-alletra-iscsi-secrets
type: Opaque
stringData:
  alletra-iscsi: |
    [alletra-iscsi]
    hpe3par_api_url=https://<your-storage-array-ip>/api/v1
    hpe3par_username=<username>
    hpe3par_password=<password>
    hpe3par_cpg=<cpg-name>
    hpe3par_iscsi_ips=<iscsi-ip1>,<iscsi-ip2>
    hpe3par_cpg_snap=<snap-cpg-name>
    san_ip=<your-storage-array-ip>
    san_login=<username>
    san_password=<password>
    [alletra-iscsi-1]
    hpe3par_api_url=https://<your-storage-array-ip>/api/v1
    hpe3par_username=<username>
    hpe3par_password=<password>
    hpe3par_cpg=<cpg-name>
    hpe3par_iscsi_ips=<iscsi-ip1>,<iscsi-ip2>
    hpe3par_cpg_snap=<snap-cpg-name>
    san_ip=<your-storage-array-ip>
    san_login=<username>
    san_password=<password>
```

Save this file and apply:

```
$ oc apply -f cinder-volume-alletra-iscsi-secret.yaml
```

__Update the OpenStack Control Plane__

To configure a single HPE Cinder backend, Open your OpenStackControlPlane CR file (```openstack_control_plane.yaml```), and add the following parameters to the cinder template. One can add multiple instances of backends in the following section:

__iSCSI driver example:__

```
spec:
      ...
      cinder:
        template:
          cinderAPI:
            ...
          cinderScheduler:
            ...
          cinderBackup:
            ...
          cinderVolumes:
            alletra-iscsi:
              customServiceConfig: |
                [DEFAULT]
                enabled_backends=alletra-iscsi
                [alletra-iscsi]
                volume_driver=cinder.volume.drivers.hpe.hpe_3par_iscsi.HPE3PARISCSIDriver
              customServiceConfigSecrets:
              - cinder-volume-alletra-iscsi-secrets
              networkAttachments:
              - storage
              replicas: 1
              resources: {}
            alletra-iscsi-1:
              customServiceConfig: |
                [DEFAULT]
                enabled_backends=alletra-iscsi
                [alletra-iscsi-1]
                volume_driver=cinder.volume.drivers.hpe.hpe_3par_iscsi.HPE3PARISCSIDriver
                volume_backend_name = alletra-iscsi-1
              customServiceConfigSecrets:
              - cinder-volume-alletra-iscsi-secrets-1
              networkAttachments:
              - storage
              replicas: 1
              resources: {}
 
        ...
```

__FC driver example:__

```
spec:
      ...
      cinder:
        template:
          cinderAPI:
            ...
          cinderScheduler:
            ...
          cinderBackup:
            ...
          cinderVolumes:
            alletra-fc:
              customServiceConfig: |
                [DEFAULT]
                enabled_backends=alletra-fc
                [alletra-fc]
                volume_driver=cinder.volume.drivers.hpe.hpe_3par_fc.HPE3PARFCDriver
              customServiceConfigSecrets:
              - cinder-volume-alletra-fc-secrets
              networkAttachments:
              - storage
              replicas: 1
              resources: {}
            alletra-fc-1:
              customServiceConfig: |
                [DEFAULT]
                enabled_backends=alletra-fc
                [alletra-fc-1]
                volume_driver=cinder.volume.drivers.hpe.hpe_3par_fc.HPE3PARFCDriver
                volume_backend_name = alletra-fc-1
              customServiceConfigSecrets:
              - cinder-volume-alletra-fc-secrets-1
              networkAttachments:
              - storage
              replicas: 1
              resources: {}
 
        ...
```

The above examples are for two backends. Notice that the Cinder configuration part of the deployment (alletra-iscsi/ alletra-iscsi-1) here must match the names used in the *OpenStackVersion* above):

Save this file and update:

```
$ oc apply -f openstack_control_plane.yaml
```

__Test the Deployed Back End__

After RHOSO openstackcontrolplane is deployed, run the following command to check if the Cinder services are up. Creating the control plane also creates an OpenStackClient pod that you can access through a remote shell (rsh) to run OpenStack CLI commands.

```
$ oc rsh openstackclient
sh-5.1$ openstack volume service list
+------------------+---------------------------------------------+------+---------+-------+----------------------------+
| Binary           | Host                                        | Zone | Status  | State | Updated At                 |
+------------------+---------------------------------------------+------+---------+-------+----------------------------+
| cinder-scheduler | cinder-scheduler-0                          | nova | enabled | up    | 2025-04-25T19:35:03.000000 |
| cinder-backup    | cinder-backup-0                             | nova | enabled | up    | 2025-04-25T19:35:06.000000 |
| cinder-volume    | cinder-volume-alletra-iscsi-0@alletra-iscsi | nova | enabled | up    | 2025-04-25T19:35:06.000000 |
+------------------+---------------------------------------------+------+---------+-------+----------------------------+
```

Check if a volume type 'alletra-iscsi' is created:

```
sh-5.1$ openstack volume type show alletra-iscsi
+--------------------+--------------------------------------+
| Field              | Value                                |
+--------------------+--------------------------------------+
| access_project_ids | None                                 |
| description        | None                                 |
| id                 | 659351fd-87dc-4abd-94e3-37f50ab2667e |
| is_public          | True                                 |
| name               | alletra-iscsi                        |
| properties         | volume_backend_name='alletra-iscsi'  |
| qos_specs_id       | None                                 |
+--------------------+--------------------------------------+
 
sh-5.1$ openstack volume type list
+--------------------------------------+---------------+-----------+
| ID                                   | Name          | Is Public |
+--------------------------------------+---------------+-----------+
| 659351fd-87dc-4abd-94e3-37f50ab2667e | alletra-iscsi | True      |
```

Create a volume using the volume type created above without error to ensure the availability of the backend:

```
sh-5.1$ openstack volume create --type alletra-iscsi --size 5 alletra-vol1
+---------------------+--------------------------------------+
| Field               | Value                                |
+---------------------+--------------------------------------+
| attachments         | []                                   |
| availability_zone   | nova                                 |
| bootable            | false                                |
| consistencygroup_id | None                                 |
| created_at          | 2025-04-25T19:44:34.787543           |
| description         | None                                 |
| encrypted           | False                                |
| id                  | cfb5e913-d5e8-48bf-a0cd-0ad41862f390 |
| migration_status    | None                                 |
| multiattach         | False                                |
| name                | alletra-vol1                         |
| properties          |                                      |
| replication_status  | None                                 |
| size                | 5                                    |
| snapshot_id         | None                                 |
| source_volid        | None                                 |
| status              | creating                             |
| type                | alletra-iscsi                        |
| updated_at          | None                                 |
| user_id             | d9e4b7975686491d83806046c1725949     |
+---------------------+--------------------------------------+
```

Confirm the volume was created successfully:

```
sh-5.1$ openstack volume list
+--------------------------------------+--------------+-----------+------+-------------+
| ID                                   | Name         | Status    | Size | Attached to |
+--------------------------------------+--------------+-----------+------+-------------+
| cfb5e913-d5e8-48bf-a0cd-0ad41862f390 | alletra-vol1 | available |    5 |             |
+--------------------------------------+--------------+-----------+------+-------------+
```


