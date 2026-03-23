# Jitsi Setup and Usage Notes

## Requirements

Refer to [Requirements](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-requirements)

### Network Link

> For a friends/small organization server, 1 Gbits/s will often be enough but for a serious server 10 Gbits/s is advisable.

Hetzner:

> The host systems for our Cloud instances all have a redundant 10 Gbits connection. This connection is shared by all instances on the host. We do not offer bandwidth guarantees for our Cloud servers, but you can expect about 300-500 Mbits. ([Regular Performance Cloud Servers](https://www.hetzner.com/cloud/regular-performance))

Test using iperf3: see [How to Test VPS Bandwidth Using iperf3 with UFW IP Whitelisting](iperf3_ip_whitelisting.md)

Result of test with parallel streams (4 threads):

```log
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec  8.88 MBytes  7.44 Mbits/sec    9             sender
[  5]   0.00-10.09  sec  8.62 MBytes  7.17 Mbits/sec                  receiver
[  7]   0.00-10.00  sec  14.8 MBytes  12.4 Mbits/sec   10             sender
[  7]   0.00-10.09  sec  13.9 MBytes  11.5 Mbits/sec                  receiver
[  9]   0.00-10.00  sec  12.0 MBytes  10.1 Mbits/sec    7             sender
[  9]   0.00-10.09  sec  11.6 MBytes  9.67 Mbits/sec                  receiver
[ 11]   0.00-10.00  sec  11.2 MBytes  9.43 Mbits/sec    8             sender
[ 11]   0.00-10.09  sec  10.8 MBytes  8.94 Mbits/sec                  receiver
[SUM]   0.00-10.00  sec  46.9 MBytes  39.3 Mbits/sec   34             sender
[SUM]   0.00-10.09  sec  44.9 MBytes  37.3 Mbits/sec                  receiver
```

This shows a total of just under 40 Mbits/sec meaning that four users will not be able to use 4k, but more than that will be able to use 720p quite comfortably.

## Ubuntu Server

Refer to [Self-Hosting Guide - Debian/Ubuntu server](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-quickstart)

- Install required packages (`gnupg2`, `nginx-full`, etc)
- Domain and DNS
- Add Prosody package repo
- Add Jitsi package rep
- Configure firewall

**Before installing: [Access Control](https://jitsi.github.io/handbook/docs/devops-guide/secure-domain/)** (if server is open to the world)

Note that installing `jitsi-meet` (below) will also create the nginx virtual host, so set up authentication (access control above) first.
