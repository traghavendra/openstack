# Extra Spec Options

The Nimble volume driver also supports the following extra spec options:

__nimble:encryption=yes__

  Used to enable encryption for a volume-type.

__nimble:perfpol-name=PERF_POL_NAME__

  PERF_POL_NAME is the name of a performance policy which exists on the
  Nimble/Alletra 6k array and should be enabled for every volume in a
  volume type.

Note:

When upgrading to OpenStack deployment to Victoria or later,
do unset ``nimble:multi-initiator`` extra-spec and set ``multiattach='<is> True'``.

__nimble:dedupe=true__

  Used to enable dedupe support for a volume-type.

__nimble:iops-limit=IOPS_LIMIT__

  Used to set the IOPS_LIMIT between 256 and 4294967294 for all
  volumes created for this volume-type.

__nimble:folder=FOLDER_NAME__

  FOLDER_NAME is the name of the folder which exists on the Nimble/Alletra 6k
  array and should be enabled for every volume in a volume type

These extra-specs can be enabled by using the following command:

```
$ openstack volume type set --property KEY=VALUE VOLUME_TYPE
```

``VOLUME_TYPE`` is the Nimble volume type and ``KEY`` and ``VALUE`` are
the options mentioned above.

