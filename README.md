# Jitsi Setup and Usage Notes

## Requirements

Refer to [Requirements](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-requirements)

### Network Link

> For a friends/small organization server, 1 Gbits/s will often be enough but for a serious server 10 Gbits/s is advisable.

Hetzner:

> The host systems for our Cloud instances all have a redundant 10 Gbits connection. This connection is shared by all instances on the host. We do not offer bandwidth guarantees for our Cloud servers, but you can expect about 300-500 Mbits. ([Regular Performance Cloud Servers](https://www.hetzner.com/cloud/regular-performance))

Test using iperf3: see [How to Test VPS Bandwidth Using iperf3 with SSH Tunneling](iperf3_ssh_tunneling.md)
