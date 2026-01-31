# Networking Advanced Cheat Sheet

## 1. Network Interfaces
```
ip addr show
ip link show
```
- Configure interface:
```
ip addr add 192.168.1.10/24 dev eth0
ip link set eth0 up
```
- Remove IP:
```
ip addr del 192.168.1.10/24 dev eth0
```

## 2. Routing
```
ip route show
ip route add default via 192.168.1.1
ip route del default
```
- Add static routes:
```
ip route add 10.0.0.0/24 via 192.168.1.1
```

## 3. DNS
- Check resolver:
```
cat /etc/resolv.conf
```
- Test DNS:
```
dig example.com
nslookup example.com
```

## 4. Connections & Ports
```
ss -tulwn        # all sockets
netstat -tulnp    # legacy
lsof -i :80       # find process using port
```

## 5. Firewall & nftables
### Example: NAT + basic rules
```
nft add table inet filter
nft add chain inet filter input { type filter hook input priority 0; policy drop; }
nft add chain inet filter forward { type filter hook forward priority 0; policy drop; }
nft add chain inet filter output { type filter hook output priority 0; policy accept; }
nft add rule inet filter input ct state established,related accept
nft add rule inet filter input tcp dport 22 accept

# NAT
nft add table ip nat
nft add chain ip nat prerouting { type nat hook prerouting priority 0; }
nft add chain ip nat postrouting { type nat hook postrouting priority 100; }
nft add rule ip nat postrouting oif "eth0" masquerade
```

## 6. Packet Analysis
```
tcpdump -i eth0
tcpdump -nn -X port 80
wireshark           # GUI
```

## 7. Bandwidth Monitoring
```
iftop -i eth0
nethogs
bmon
```

## 8. Network Namespaces & Virtualization
```
ip netns add ns1
ip link set veth0 netns ns1
ip netns exec ns1 ip addr add 10.0.0.1/24 dev veth0
ip netns exec ns1 ip link set veth0 up
```

## 9. NAT, Port Forwarding & Masquerade
- Using nftables (see above)
- Port forward example:
```
nft add rule ip nat prerouting iif "eth0" tcp dport 2222 dnat to 10.0.0.2:22
```

## 10. VLANs
```
ip link add link eth0 name eth0.10 type vlan id 10
ip addr add 192.168.10.1/24 dev eth0.10
ip link set eth0.10 up
```

## 11. Bonding / Teaming
```
modprobe bonding
ip link add bond0 type bond
ip link set eth1 master bond0
ip link set eth2 master bond0
ip link set bond0 up
```

## 12. Advanced Routing & Policy Routing
```
ip rule add from 192.168.1.100 table 100
ip route add default via 192.168.1.1 table 100
ip rule show
ip route show table 100
```

## 13. ARP & MAC Management
```
arp -n
ip neigh show
ip link set dev eth0 address 00:11:22:33:44:55
```

## 14. Troubleshooting Commands
```
ping 8.8.8.8
traceroute example.com
mtr example.com
curl -v http://example.com
wget --spider http://example.com
```

## 15. Useful Tips
- Use `watch -n1 ss -tulwn` to monitor sockets.
- Combine `tcpdump` with `grep` for live filtering.
- Use network namespaces to safely test configs.
- Save nftables rules for persistence:
```
nft list ruleset > /etc/nftables.conf
systemctl enable nftables
```
- Use `ip monitor all` to watch IP/route changes in real-time.

