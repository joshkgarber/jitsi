# How to Test VPS Bandwidth Using iperf3 with SSH Tunneling

## Introduction

Testing your VPS bandwidth is essential for ensuring your server meets performance requirements. iperf3 is a powerful tool that measures network bandwidth, while SSH tunneling provides a secure method to run these tests. This guide walks you through the process step-by-step.

## Prerequisites

- Two VPS instances (server and client) with root or sudo access
- SSH access to both systems
- Basic command-line knowledge
- iperf3 installed on both systems

## Step 1: Install iperf3

On both your server and client VPS, install iperf3:

**For Debian/Ubuntu:**
```bash
sudo apt-get update
sudo apt-get install iperf3
```

**For CentOS/RHEL:**
```bash
sudo yum install iperf3
```

**For Alpine:**
```bash
apk add iperf3
```

Verify installation:
```bash
iperf3 --version
```

## Step 2: Start iperf3 Server on Your VPS

On your server VPS, start iperf3 in server mode, listening on localhost only:

```bash
iperf3 -s -B 127.0.0.1
```

**Parameters explained:**
- `-s`: Run in server mode
- `-B 127.0.0.1`: Bind to localhost (secure, won't expose to the internet)

The server will display:
```
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
```

## Step 3: Create SSH Tunnel from Client to Server

On your client VPS, establish an SSH tunnel that forwards a local port to the iperf3 server:

```bash
ssh -L 5201:127.0.0.1:5201 user@server_ip -N
```

**Parameters explained:**
- `-L 5201:127.0.0.1:5201`: Forward local port 5201 to server's localhost:5201
- `user@server_ip`: SSH credentials to your server VPS
- `-N`: Don't execute remote commands, just forward ports

Keep this terminal window open while testing.

## Step 4: Run iperf3 Client Test

In a new terminal on your client VPS, run the iperf3 client:

```bash
iperf3 -c 127.0.0.1 -t 10
```

**Parameters explained:**
- `-c 127.0.0.1`: Connect to localhost (which tunnels to the server)
- `-t 10`: Run test for 10 seconds

## Step 5: Analyze Results

The client will display results similar to:

```
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec  12.5 MBytes   105 Mbits/sec    0   329 KBytes
[  5]   1.00-2.00   sec  12.3 MBytes   103 Mbits/sec    0   329 KBytes
...
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec   124 MBytes   104 Mbits/sec    0
```

**Key metrics:**
- **Bitrate**: Your bandwidth in Mbits/sec
- **Retr**: Number of retransmissions (lower is better)
- **Transfer**: Total data transferred

## Advanced Testing Options

### Test Reverse Direction (Server to Client)

For a bidirectional test, use the `-R` flag:

```bash
iperf3 -c 127.0.0.1 -t 10 -R
```

### Test Multiple Parallel Connections

Run multiple simultaneous connections to stress-test bandwidth:

```bash
iperf3 -c 127.0.0.1 -t 10 -P 4
```

The `-P 4` flag runs 4 parallel streams.

### UDP Testing

Test UDP bandwidth instead of TCP:

```bash
iperf3 -c 127.0.0.1 -t 10 -u -b 1G
```

- `-u`: Use UDP protocol
- `-b 1G`: Set bandwidth target to 1 Gbps

### Longer Test Duration

For more accurate results, increase test duration:

```bash
iperf3 -c 127.0.0.1 -t 60 -i 10
```

The `-i 10` flag prints results every 10 seconds.

## Troubleshooting

**SSH tunnel fails to connect:**
- Verify SSH credentials: `ssh user@server_ip`
- Check firewall rules on the server
- Ensure SSH service is running

**iperf3 connection refused:**
- Confirm iperf3 server is running: `ps aux | grep iperf3`
- Verify tunnel is active and listening on port 5201

**Unusually low bandwidth:**
- Stop other services consuming bandwidth
- Run tests during off-peak hours
- Check network interface status: `ifconfig` or `ip link`

## Cleanup

When finished testing, stop the iperf3 server:

```bash
# On server VPS, press Ctrl+C or run:
pkill iperf3
```

Close the SSH tunnel by pressing `Ctrl+C` in the tunnel terminal.

## Conclusion

Testing VPS bandwidth with iperf3 and SSH tunneling provides accurate, secure performance measurements. Regular testing helps monitor your infrastructure's health and identify potential issues before they impact your services.
