# Consistency Group Replication

The Nimble driver supports replication for consistency groups.

- Volumes can be replicated synchronously or asynchronously.
- Requires replication partnership between arrays.
- Configure in Cinder with:
  ```ini
  replication_device = backend_id:remote_nimble, san_ip:<ip>, san_login:<user>, san_password:<pass>
  ```
