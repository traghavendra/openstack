# Extra Specs & QoS

HPE drivers support `extra-specs` to control volume characteristics.

__Common Extra-Specs__

- `hpe3par:cpg=<cpg-name>`
- `hpe3par:flash_cache=true|false`
- `hpe3par:compression=true|false`
- `hpe3par:qos=<qos-id>`

__QoS__

- Define QoS rules in the array.
- Map volume types to QoS profiles via extra-specs.

