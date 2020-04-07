# Ubuntu MAAS 2.7.0 Wake on LAN Driver Patch
The WOL driver has indeed been removed from MAAS 2.0, but WoL functionality is still needed.\
This patch is based on [Using Wake on LAN with MAAS 2.x](https://stgraber.org/2017/04/02/using-wake-on-lan-with-maas-2-x/), 
[Ubuntu MAAS 2.2+ Wake on LAN Driver Patch](https://github.com/kairen/MAAS-WoL-driver) and 
[Ubuntu MAAS 2.2 Wake on LAN Driver Patch](https://github.com/yosefrow/MAAS-WoL-driver).

This patch uses wakeonlan package, but can be modified to use etherwake or powerwake in the wol_cmd variable. Though etherwake needs sudo added to the command variable.

Requirement:
* Install the wakeonlan  package (or etherwake, powerwake on your choice)
* Install MAAS

Patch the WOL driver into MAAS as follows:

```sh
$ sudo apt-get install -y wakeonlan
$ PATCH_DIR="/usr/lib/python3/dist-packages/provisioningserver/"
$ sudo patch -p1 -d ${PATCH_DIR} < maas-wol.diff 
$ sudo systemctl restart maas-rackd maas-regiond
```


## Note for etherwake
etherwake uses udp which requires it to run as root. The fix is to allow maas to become root.

run `sudo visudo`

add the line

`maas ALL= NOPASSWD: /usr/sbin/etherwake`

where /usr/sbin/etherwake is the result of `which etherwake`

## Note to enable WOL host
WOL on the host can be enabled by adding this udev rule.

/etc/udev/rules.d/wol.rules
```
ACTION=="add", SUBSYSTEM=="net", NAME=="en*", RUN+="/sbin/ethtool -s $name wol g"
```

