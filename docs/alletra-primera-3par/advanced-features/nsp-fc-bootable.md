# NSP for FC Bootable Volumes

Given a system connected to HPE 3PAR via FC and multipath setting is NOT used in cinder.conf. When the user tries to create a bootable volume, it fails intermittently with the following error: Fibre Channel volume device not found

This happens when a zone is created using second or later target from 3PAR backend. In this case, HPE 3PAR client code picks up first target to form initiator target map. This can be illustrated with below example.

Sample output of showport command:

```
$ showport -sortcol 6
```

```
N:S:P      Mode State ----Node_WWN---- -Port_WWN/HW_Addr-  Type Protocol Partner FailoverState
0:1:1    target ready 2FF70002AC002DB6   20110002AC002DB6  host       FC       -             -
0:1:2    target ready 2FF70002AC002DB6   20120002AC002DB6  host       FC   1:1:2          none
1:1:1 initiator ready 2FF70002AC002DB6   21110002AC002DB6  rcfc       FC       -             -
1:1:2    target ready 2FF70002AC002DB6   21120002AC002DB6  host       FC   0:1:2          none
2:1:1 initiator ready 2FF70002AC002DB6   22110002AC002DB6  rcfc       FC       -             -
2:1:2    target ready 2FF70002AC002DB6   22120002AC002DB6  host       FC   3:1:2          none
3:1:1    target ready 2FF70002AC002DB6   23110002AC002DB6  host       FC       -             -
3:1:2    target ready 2FF70002AC002DB6   23120002AC002DB6  host       FC   2:1:2          none
```

Suppose zone is created using targets “2:1:2” and “3:1:2” from above output. Then initiator target map is created using target “0:1:1” only. In such a case, the path is not found, and bootable volume creation fails.

To avoid above mentioned failure, the user can specify the target in 3PAR backend section of cinder.conf as follows:

**hpe3par_target_nsp = 3:1:2**

Using above mentioned nsp, respective wwn information is fetched. Later initiator target map is created using wwn information and bootable volume is created successfully.

Note: If above mentioned option (nsp) is not specified in cinder.conf, then the original flow is executed i.e first target is picked and bootable volume creation may fail.

