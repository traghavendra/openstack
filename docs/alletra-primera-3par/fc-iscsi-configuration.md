# FC / iSCSI Configuration

## Fibre Channel
- Ensure proper zoning between hosts and array ports.
- Mask volumes using WWNs.

## iSCSI
- Configure multipathd.
- Ensure initiators are registered on the array.
- Example config snippet in `/etc/multipath.conf`:
  ```ini
  defaults {
      user_friendly_names yes
      find_multipaths yes
  }
  ```

## Notes
- Use CHAP for authentication in iSCSI.
- Multipathing ensures resiliency.
