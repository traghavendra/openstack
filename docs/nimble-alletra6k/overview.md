# Overview

The HPE Nimble / Alletra 6000 driver integrates with OpenStack Cinder
to provide block storage over iSCSI and FC using NimbleISCSIDriver
and NimbleFCDriver

__Supported Backends__

- Nimble OS 5.3 or later
- Alletra 6000 OS 6.0 or later

Nimble and Alletra 6000 Storage Cinder drivers do not support port binding
with multiple interfaces on the same subnet due to existing limitation in
os-brick.


