# RHOSP 18 Deployment Guide (HPE Alletra MP)

## Prerequisites
- RHOSP 18 installed
- Alletra MP reachable
- Required packages installed

## Deployment Steps
1. Clone:
   ```bash
   git clone https://github.com/hpe-storage/hpe-openstack-docs.git
   cd hpe-openstack-docs/hpe-alletra-mp-rhoso18
   ```
2. Edit environment files for your array.
3. Deploy with:
   ```bash
   openstack overcloud deploy -e hpe-alletra-mp.yaml
   ```

## References
- [GitHub repo](https://github.com/hpe-storage/hpe-openstack-docs/tree/main/hpe-alletra-mp-rhoso18)
