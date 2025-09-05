# Configuration

Example `cinder.conf`:

```ini
[DEFAULT]
enabled_backends = nimble

[nimble]
volume_driver = cinder.volume.drivers.nimble.NimbleISCSIDriver
san_ip = <array-ip>
san_login = <user>
san_password = <pass>
nimble_pool_name = default
```

Restart Cinder after configuration.
