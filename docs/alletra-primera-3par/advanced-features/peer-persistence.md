# Peer Persistence

HPE Peer Persistence provides:
- Transparent failover between two arrays
- Zero RPO (synchronous replication)
- Continuous availability during site failures

Enable replication and configure Peer Persistence in Cinder by setting:

```ini
replication_device = backend_id:remote_array, san_ip:<ip>, san_login:<user>, san_password:<pass>
```
