# System Requirements

## Software Requirements
- HPE 3PAR OS v3.1.3 MU1 or later.
- Deduplication: requires SSDs and OS v3.2.1 MU1+.
- Flash Cache Policy: OS v3.2.1 MU2+, Flash Cache enabled, python-3parclient ≥ 4.2.0.
- Compression: requires SSDs and OS v3.3.1 MU1+ (8k/20k series).
- Primera / Alletra MP: python-3parclient ≥ 4.2.14.

## Network Requirements
- iSCSI: multipathing strongly recommended.
- FC: zoning between initiators and storage ports.
- WSAPI server must be enabled.

## Storage Requirements
- At least one Common Provisioning Group (CPG).
- Licenses for Peer Persistence and replication (if used).
