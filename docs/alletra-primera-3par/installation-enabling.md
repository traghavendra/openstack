# Installation & Enabling the Driver

1. Install required packages:

   ```
   pip install python-3parclient
   ```

2. Edit ```cinder.conf``` file:

```
[DEFAULT]
enabled_backends = hpe3par

[hpe3par]
volume_driver = cinder.volume.drivers.hpe.hpe_3par_fc.HPE3PARFCDriver
san_ip = <array-ip>
san_login = <username>
san_password = <password>
hpe3par_api_url = https://<array-ip>:8080/api/v1
hpe3par_cpg = <CPG-name>
```

3. Restart Cinder services:

   ```
   systemctl restart openstack-cinder-volume
   ```

