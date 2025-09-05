# RHOSP 17 Deployment Guide (HPE 3PAR / Primera / Alletra 9000)

## Prerequisites
- RHOSP 17 installed
- HPE storage reachable
- Required packages:
  - python-3parclient
  - hpe3parclient

## Deployment Steps
1. Clone:
   ```bash
   git clone https://github.com/hpe-storage/hpe-3par-cinder-rhosp17.git
   ```
2. Edit environment files with array IPs and credentials.
3. Deploy with:
   ```bash
   openstack overcloud deploy -e hpe3par-cinder.yaml
   ```

## References
- [GitHub repo](https://github.com/hpe-storage/hpe-3par-cinder-rhosp17)
