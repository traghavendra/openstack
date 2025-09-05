# RHOSP 18 Deployment Guide (HPE Alletra MP)

__Prerequisites__

- RHOSP 18 installed
- Alletra MP reachable
- Required packages installed

__Deployment Steps__

1. Clone:

   ```
   git clone https://github.com/hpe-storage/hpe-openstack-docs.git
   cd hpe-openstack-docs/hpe-alletra-mp-rhoso18
   ```

2. Edit environment files for your array.

3. Deploy with:

   ```
   openstack overcloud deploy -e hpe-alletra-mp.yaml
   ```

__References__

- [GitHub repo](https://github.com/hpe-storage/hpe-openstack-docs/tree/main/hpe-alletra-mp-rhoso18)

