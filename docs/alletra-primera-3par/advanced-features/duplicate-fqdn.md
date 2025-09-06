# Duplicate FQDN Support

The 3PAR driver uses the FQDN of the node that is doing the attach as an unique identifier to map the volume.

The problem is that the FQDN is not always unique, there are environments where the same FQDN can be found in different systems, and in those cases if both try to attach volumes the second system will fail.

One example of this happening would be on a QA environment where you are creating VMs and they all have names like controller-0.localdomain and compute-0.localdomain.

To support these kind of environments, the user can specify below flag in backend_defaults section or the specific cinder driver section of cinder.conf as follows:

**unique_fqdn_network = False**

When this flag is used, then during attach volume to instance, iscsi initiator name is used instead of FQDN.

If above mentioned flag is not specified in cinder.conf, then its value is considered as True (by default) and FQDN is used (existing behavior).

