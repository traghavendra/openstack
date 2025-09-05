# Multipathing

HPE recommends enabling multipath I/O for all Nimble volumes.

- iSCSI: configure multipathd
- FC: ensure proper zoning

Check with:
```bash
multipath -ll
```
