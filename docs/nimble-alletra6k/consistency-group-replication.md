# Consistency Group Replication

To enable consistency group replication, follow below steps:

1] Add `replication_device` to storage backend settings in `cinder.conf`, then
restart Cinder Volume service.

Example of `cinder.conf` for volume replications:

```
[nimble]
san_ip = xxx.xxx.xxx.xxx
...
replication_device = backend_id:nimblevsagroup2,
                     san_ip:10.132.239.66,
                     san_login:admin,
                     san_password:admin,
                     schedule_name:sched-one,
                     downstream_partner:nimblevsagroup2,
                     period:15,
                     period_unit:minutes
```

- Only one `replication_device` can be configured for each primary backend.
- Keys `backend_id`, `san_ip`, `san_login`, `san_password`, `schedule_name`
  and `downstream_partner` are mandatory.
- Other parameters are optional (if not given, then default values
  will be used):

  period:1

  period_unit:days

  num_retain:10

  num_retain_replica:1

  at_time:'00:00'

  until_time:'23:59'

  days='all'

  replicate_every:1

  alert_threshold:'24:00'

2] Create a volume type with properties ``replication_enabled='<is> True'``
and ``consistent_group_snapshot_enabled='<is> True'``

```
$ cinder type-create nimble
$ cinder type-key nimble set volume_backend_name='nimble'
$ cinder type-key nimble set replication_enabled='<is> True'
$ cinder type-key nimble set consistent_group_snapshot_enabled='<is> True'
```

3] Create a consistency group type with properties

``consistent_group_snapshot_enabled='<is> True'``
and ``consistent_group_replication_enabled='<is> True'``.

```
$ cinder --os-volume-api-version 3.38 group-type-create repl_type
$ cinder --os-volume-api-version 3.38 group-type-key repl_type set
consistent_group_snapshot_enabled='<is> True' consistent_group_replication_enabled='<is> True'
```

4] Create a group type with volume types support replication.

```
$ cinder --os-volume-api-version 3.38 group-create --name grp_1 repl_type nimble
```

5] Create volume in the consistency group.

```
$ cinder --os-volume-api-version 3.38 create --volume-type nimble --group-id {grp_1-id}
--name {volume-name} {size}
```

6] Enable consistency group replication.

```
$ cinder --os-volume-api-version 3.38 group-enable-replication grp_1
```

7] Disable consistency group replication.

```
$ cinder --os-volume-api-version 3.38 group-disable-replication grp_1
```

8] Failover consistency group replication.

```
$ cinder --os-volume-api-version 3.38 group-failover-replication
--secondary-backend-id nimblevsagroup2 grp_1
```

9] Failback consistency group replication.

```
$ cinder --os-volume-api-version 3.38 group-failover-replication
--secondary-backend-id default grp_1
```

