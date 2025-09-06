# Configuration

Update the file ``/etc/cinder/cinder.conf`` with the given configuration.
Note: These parameters apply to Alletra 6k Storage as well.

In case of a basic (single back-end) configuration, add the parameters
within the ``[default]`` section as follows.

```
[default]
san_ip = NIMBLE_MGMT_IP
san_login = NIMBLE_USER
san_password = NIMBLE_PASSWORD
use_multipath_for_image_xfer = True
volume_driver = NIMBLE_VOLUME_DRIVER
san_thin_provision = True
```

In case of multiple back-end configuration, for example, configuration
which supports multiple Nimble Storage arrays or a single Nimble Storage
array with arrays from other vendors, use the following parameters.

```
[default]
enabled_backends = Nimble-Cinder

[Nimble-Cinder]
san_ip = NIMBLE_MGMT_IP
san_login = NIMBLE_USER
san_password = NIMBLE_PASSWORD
use_multipath_for_image_xfer = True
volume_driver = NIMBLE_VOLUME_DRIVER
volume_backend_name = NIMBLE_BACKEND_NAME
```

In case of multiple back-end configuration, Nimble Storage volume type
is created and associated with a back-end name as follows.

Note:

   Single back-end configuration users do not need to create the volume type.

```
   $ openstack volume type create NIMBLE_VOLUME_TYPE
   $ openstack volume type set --property volume_backend_name=NIMBLE_BACKEND_NAME NIMBLE_VOLUME_TYPE
```

This section explains the variables used above:

__NIMBLE_MGMT_IP__

  Management IP address of Nimble/Alletra 6k Storage array/group.

__NIMBLE_USER__

  Nimble/Alletra 6k Storage account login with minimum ``power user``
  (admin) privilege if RBAC is used.

__NIMBLE_PASSWORD__

  Password of the admin account for Nimble/Alletra 6k array.

__NIMBLE_VOLUME_DRIVER__

  Use either cinder.volume.drivers.hpe.nimble.NimbleISCSIDriver for iSCSI or
  cinder.volume.drivers.hpe.nimble.NimbleFCDriver for Fibre Channel.

__NIMBLE_BACKEND_NAME__

  A volume back-end name which is specified in the ``cinder.conf`` file.
  This is also used while assigning a back-end name to the Nimble volume type.

__NIMBLE_VOLUME_TYPE__

  The Nimble volume-type which is created from the CLI and associated with
  ``NIMBLE_BACKEND_NAME``.

Note:

Restart the ``cinder-api``, ``cinder-scheduler``, and ``cinder-volume``
services after updating the ``cinder.conf`` file.

