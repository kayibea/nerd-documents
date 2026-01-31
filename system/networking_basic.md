# Bash/Linux Networking Cheat Sheet

## 1. Basic Network Info

| Command | Description | Tricks |
|---------|------------|--------|
| `ip a` / `ip addr` | Show IP addresses and interfaces | `ip addr show dev eth0` for a specific interface |
| `ip link` | Show interface status | `ip link set eth0 up/down` to enable/disable interface |
| `ifconfig` | Legacy interface info | `ifconfig -a` shows all interfaces |
| `hostname` | Show or set hostname | `hostnamectl set-hostname myhost` |
| `ping` | Test connectivity | `ping -c 4 google.com` (limit to 4 packets) |
| `traceroute` | Trace route to host | `traceroute google.com` (may require installation) |
| `mtr` | Interactive traceroute + ping | `mtr google.com |

## 2. DNS & Name Resolution

| Command | Description | Tricks |
|---------|------------|--------|
| `nslookup` | Query DNS | `nslookup google.com` |
| `dig` | Detailed DNS queries | `dig +short google.com`, `dig MX gmail.com` for mail servers |
| `host` | Simple DNS lookup | `host google.com` |
| `resolvectl` / `systemd-resolve` | Query and manage systemd-resolved | `resolvectl status` |

## 3. Routing & Gateways

| Command | Description | Tricks |
|---------|------------|--------|
| `ip route` | Show routing table | `ip route show default` for default gateway |
| `route` | Legacy routing info | `route -n` (numeric, no DNS) |
| `ip route add` / `del` | Add/delete routes | `ip route add 192.168.2.0/24 via 192.168.1.1` |

## 4. Network Connections & Listening Ports

| Command | Description | Tricks |
|---------|------------|--------|
| `ss` | Show sockets | `ss -tuln` (TCP/UDP listening, numeric) |
| `netstat` | Legacy socket info | `netstat -tulnp` (processes + ports) |
| `lsof -i` | Show open network files | `lsof -i :80` (processes using port 80) |
| `nmap` | Scan hosts & ports | `nmap 192.168.1.0/24`, `nmap -p 22-80 host` |

## 5. File Transfers

| Command | Description | Tricks |
|---------|------------|--------|
| `scp` | Secure copy over SSH | `scp file user@host:/path` |
| `rsync` | Efficient file copy/sync | `rsync -avz /src/ user@host:/dest/` |
| `wget` | Download files | `wget https://example.com/file` |
| `curl` | Transfer data | `curl -O https://example.com/file`, `curl -I https://example.com` for headers |

## 6. Firewalls & Security

| Command | Description | Tricks |
|---------|------------|--------|
| `nft` | Modern packet filtering tool | `nft list ruleset` to view, `nft add rule inet filter input tcp dport 22 accept` to allow SSH, `nft add rule inet filter input ip saddr 1.2.3.4 drop` to block an IP |
| `ufw` | Uncomplicated firewall | `ufw status`, `ufw allow 22/tcp` |
| `firewalld` | Firewall daemon | `firewall-cmd --list-all`, `firewall-cmd --add-service=http` |

## 7. Monitoring & Traffic

| Command | Description | Tricks |
|---------|------------|--------|
| `tcpdump` | Capture packets | `tcpdump -i eth0 port 80` (requires root) |
| `iftop` | Monitor bandwidth usage | `iftop -i eth0` |
| `nload` | Real-time network usage | `nload eth0` |
| `ping` | Latency check | `ping -c 5 host` |
| `mtr` | Latency + route | `mtr host` (interactive) |

## 8. Wireless Networking

| Command | Description | Tricks |
|---------|------------|--------|
| `iwconfig` | Show wireless config | `iwconfig wlan0` |
| `nmcli` | NetworkManager CLI | `nmcli device wifi list`, `nmcli connection up id <name>` |
| `wpa_supplicant` | Manage Wi-Fi connections | `wpa_supplicant -B -i wlan0 -c /etc/wpa_supplicant.conf`

## 9. VPN & Tunneling

| Command | Description | Tricks |
|---------|------------|--------|
| `ssh -L` | Local port forwarding | `ssh -L 8080:localhost:80 user@remote` |
| `ssh -R` | Remote port forwarding | `ssh -R 9090:localhost:80 user@remote` |
| `openvpn` | Connect to VPN | `openvpn --config client.ovpn` |
| `ip tunnel` | Create tunnels | `ip tunnel add tun0 mode gre remote <host> local <host>`

## 10. Tips & Tricks

- Use `ip -s link` to monitor interface stats (packets, errors).
- Use `ss -s` for summary of sockets.
- Combine `grep` with `ss` or `netstat` for specific ports.
- Use `tcpdump -w file.pcap` to save capture for Wireshark.
- For quick connectivity check: `curl -I https://example.com`.
- Check DNS resolution with `dig +trace example.com`.
- Use `nmcli` or `iw dev` for wireless troubleshooting.
- For persistent firewall rules, prefer `ufw`, `firewalld`, or `nft` over raw iptables.
- Always monitor `dmesg` for network interface errors after driver changes or cable issues.