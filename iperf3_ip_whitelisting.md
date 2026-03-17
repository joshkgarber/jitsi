# How to Test VPS Bandwidth Using iperf3 with UFW IP Whitelisting

## Overview

iperf3 is a powerful tool for measuring network bandwidth and performance. This guide walks you through setting up iperf3 on a remote VPS and using UFW (Uncomplicated Firewall) to securely test bandwidth with IP whitelisting.

## Prerequisites

- Two systems: a server (VPS) and a client (your local machine)
- SSH access to your VPS
- Root or sudo privileges on both systems
- Basic command-line knowledge

## Step 1: Install iperf3

### On the VPS (Server)

```bash
sudo apt-get update
sudo apt-get install iperf3
```

### On Your Local Machine (Client)

**Ubuntu/Debian:**
```bash
sudo apt-get install iperf3
```

**macOS:**
```bash
brew install iperf3
```

**Windows:**
Download the executable from [iperf.fr](https://iperf.fr) or use WSL.

## Step 2: Configure UFW Firewall Rules

### Enable UFW (if not already enabled)

```bash
sudo ufw enable
```

### Whitelist Your Client's IP Address

Replace `YOUR_CLIENT_IP` with your actual public IP address:

```bash
sudo ufw allow from YOUR_CLIENT_IP to any port 5201 proto tcp
sudo ufw allow from YOUR_CLIENT_IP to any port 5201 proto udp
```

### Verify the Rules

```bash
sudo ufw status
```

You should see output similar to:

```
Status: active

To                         Action      From
--                         ------      ----
5201/tcp                   ALLOW       YOUR_CLIENT_IP
5201/udp                   ALLOW       YOUR_CLIENT_IP
```

## Step 3: Start iperf3 Server on VPS

SSH into your VPS and start the iperf3 server:

```bash
iperf3 -s -D
```

**Flags explained:**
- `-s` : Run in server mode
- `-D` : Run as a daemon (background process)

To verify the server is running:

```bash
ps aux | grep iperf3
```

## Step 4: Run Bandwidth Tests from Your Client

### Basic TCP Test

From your local machine, replace `VPS_IP` with your VPS's IP address:

```bash
iperf3 -c VPS_IP
```

### UDP Test

```bash
iperf3 -c VPS_IP -u
```

### Advanced Testing Options

**Adjust test duration (30 seconds):**
```bash
iperf3 -c VPS_IP -t 30
```

**Set parallel streams (4 threads):**
```bash
iperf3 -c VPS_IP -P 4
```

**Reverse direction (test VPS to client):**
```bash
iperf3 -c VPS_IP -R
```

**Bidirectional test:**
```bash
iperf3 -c VPS_IP --bidir
```

**Test with specific bandwidth limit (useful for testing QoS):**
```bash
iperf3 -c VPS_IP -b 100M
```

## Step 5: Interpret Results

iperf3 provides output like:

```
Connecting to host VPS_IP, port 5201
[  5] local CLIENT_IP port 54321 connected to VPS_IP port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec  11.2 MBytes  94.0 Mbits/sec    0   165 KBytes
[  5]   1.00-2.00   sec  12.5 MBytes  105 Mbits/sec     0   209 KBytes
...
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec   120 MBytes   100 Mbits/sec    0             sender
[  5]   0.00-10.04  sec   119 MBytes   99.5 Mbits/sec                 receiver
```

**Key metrics:**
- **Transfer:** Total data transferred
- **Bitrate:** Average speed (Mbits/sec or Gbits/sec)
- **Retr:** Number of retransmissions (lower is better)

## Step 6: Stop the iperf3 Server

```bash
pkill iperf3
```

## Security Best Practices

1. **Use strong IP whitelisting** – Only allow known IPs
2. **Rotate your client IP** – If using dynamic IP, update UFW rules regularly
3. **Monitor firewall logs** – Check for unauthorized access attempts:
   ```bash
   sudo tail -f /var/log/syslog | grep UFW
   ```

4. **Limit test windows** – Temporarily enable ports only during testing:
   ```bash
   sudo ufw delete allow from YOUR_CLIENT_IP to any port 5201
   ```

5. **Consider a VPN** – For maximum security, connect via VPN before testing

## Troubleshooting

**Connection refused?**
- Verify your IP is whitelisted: `sudo ufw status`
- Check the iperf3 server is running: `ps aux | grep iperf3`
- Ensure correct port: `sudo netstat -tlnp | grep 5201`

**Low bandwidth results?**
- Test during off-peak hours
- Run multiple tests for consistency
- Check for network congestion with `mtr` or `traceroute`

**Timeout errors?**
- Verify firewall rules allow UDP if testing UDP
- Check network MTU settings

## Conclusion

Using iperf3 with UFW IP whitelisting provides a secure, efficient method to test VPS bandwidth. Regular testing helps identify performance bottlenecks and ensures your VPS meets service requirements.
