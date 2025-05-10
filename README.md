# OSDMon

OSDMon is a simple Ceph OSD monitor with Prometheus exporter feature.

## History

* 2025/04/15 Initial version
* 2025/05/05 Split repository from junkbox

## Description

OSDMon is a simple Ceph OSD monitor with Prometheus exporter feature.

OSDMon periodically calls 'ceph osd df -f json' (default 60s) and
checks if there are osds with higher utilization than high_threshold.

If over utilized OSDs were detected, OSDMon sets reweight value
by calling 'ceph osd reweight osd.OSD_ID REWEIGHT' for the over utilized osds.

If OSDMon detected OSDs with lower utilization than low threshold
while periodical monitoring, it resets reweight value to 1.0 for OSDs.


OSDMon listens on 19199 (default) and returns utilization information
with prometheus exporter compatible format for the ceph cluster and
each osd indivisually.

TODO

* Consider cluster level high utilization case where rebalance (including
  reweight) functionality does not work.
* ...

## Installation


Just copy osdmon.py under /usr/local/bin, and set execution bits on
a server where you can run 'ceph' command.

You can also use osdmon.service for systemd service registration.

```
$ sudo cp -i osdmon.py /usr/local/bin
$ sudo chmod 755 /usr/local/bin/osdmon.py
$ sudo cp -i osdmon.service /lib/systemd/system/
$ sudo systemctl enable osdmon.service
$ sudo systemctl start osdmon.service
```

## Usage

osdmon.py takes several parameters like below.

Important arguments and their default values are:

* `-t HIGH_THRESHOLD`
  * High threshold value to make reweight value of an OSD lower. default is 94.0 (94%).
*  `-l LOW_THRESHOLD`
  * Low threshold value to reset reweight value of an OSD with lower reweight value than 1.0. default is 93.0 (93%).
* `-r TARGET_REWEIGHT`
  * reweight value to be used in order to reduce utilization of an OSD. efault is 0.8.

```
$ python3 osdmon.py -h
usage: osdmon.py [-h] [-b BIND_ADDRESS] [-p PORT] [--debug] [--dry_run]
                 [-i INTERVAL] [-t HIGH_THRESHOLD] [-l LOW_THRESHOLD]
                 [-r TARGET_REWEIGHT] [-L LOGFILE]

osdmon.py

options:
  -h, --help            show this help message and exit
  -b BIND_ADDRESS, --bind_address BIND_ADDRESS
  -p PORT, --port PORT
  --debug
  --dry_run
  -i INTERVAL, --interval INTERVAL
  -t HIGH_THRESHOLD, --high_threshold HIGH_THRESHOLD
  -l LOW_THRESHOLD, --low_threshold LOW_THRESHOLD
  -r TARGET_REWEIGHT, --target_reweight TARGET_REWEIGHT
  -L LOGFILE, --logfile LOGFILE
$
```

## License

Apache License, Version 2.0
