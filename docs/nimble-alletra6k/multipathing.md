# Multipathing

In OpenStack environments where Cinder block device multipathing is desired
there are a few things to consider.

Configuring mulitpathing varies by system depending on the environment. In a
scenario where solely Nimble devices are being created by Cinder, the
following ``/etc/multipath.conf`` file may be used:

```
defaults {
    user_friendly_names yes
    find_multipaths     no
}

blacklist {
    devnode "^(ram|raw|loop|fd|md|dm-|sr|scd|st)[0-9]*"
    devnode "^hd[a-z]"
    device {
        vendor  ".*"
        product ".*"
    }
}

blacklist_exceptions {
    device {
        vendor  "Nimble"
        product "Server"
    }
}

devices {
    device {
        vendor               "Nimble"
        product              "Server"
        path_grouping_policy group_by_prio
        prio                 "alua"
        hardware_handler     "1 alua"
        path_selector        "service-time 0"
        path_checker         tur
        features             "1 queue_if_no_path"
        no_path_retry        30
        failback             immediate
        fast_io_fail_tmo     5
        dev_loss_tmo         infinity
        rr_min_io_rq         1
        rr_weight            uniform
    }
}
```

After making changes to ``/etc/multipath.conf``, the multipath subsystem needs
to be reconfigured:

```
# multipathd reconfigure
```

Tip:

The latest best practices for Nimble devices can be found in the HPE Nimble
Storage Linux Integration Guide found on https://infosight.hpe.com

Important:

OpenStack Cinder is currently not compatible with the HPE Nimble Storage
Linux Toolkit (NLT)

Nova needs to be configured to pickup the actual multipath device created on
the host.

In ``/etc/nova/nova.conf``, add the following to the ``[libvirt]`` section:

```
[libvirt]
volume_use_multipath = True
```

Note:
In versions prior to Newton, the option was called ``iscsi_use_multipath``

After editing the Nova configuration file, the ``nova-conductor`` service
needs to be restarted.

Tip:
Depending on which particular OpenStack distribution is being used, Nova
may use a different configuration file than the default.

To validate that instances get properly connected to the multipath device,
inspect the instance devices:

```
# virsh dumpxml <Instance ID | Instance Name | Instance UUID>
```

