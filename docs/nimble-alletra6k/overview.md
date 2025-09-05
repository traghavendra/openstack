# Overview & Compatibility

The HPE Nimble / Alletra 6000 driver integrates with OpenStack Cinder
to provide block storage over iSCSI and FC.

## Supported Backends
- `NimbleISCSIDriver`
- `NimbleFCDriver`

## Limitations
- Port binding across multiple interfaces in the same subnet is not supported.
