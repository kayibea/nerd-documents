# Debugging & Diagnostics Cheat Sheet

## 1. Core Concepts
- **Debugging**: Identify and fix errors in code or system.
- **Diagnostics**: Assess system health, identify bottlenecks, or misconfigurations.

---

## 2. System Diagnostics

### top / htop
```
top               # dynamic process viewer
htop              # interactive process monitor
```
- Check CPU, memory usage, process load.
- htop: easier process selection, sorting, killing.

### vmstat
```
vmstat 1          # memory, I/O, CPU usage every 1s
```

### iostat
```
iostat -xz 1      # disk I/O stats
```

### mpstat
```
mpstat -P ALL 1   # per CPU usage
```

### free
```
free -h           # RAM and swap usage
```

### dmesg
```
dmesg | less      # kernel ring buffer messages
```
Filter for errors:
```
dmesg | grep -i error
```

---

## 3. Log Inspection
```
journalctl -xe    # recent systemd logs
journalctl -u ssh # logs for ssh service
```

---

## 4. Network Diagnostics

### ping / traceroute
```
ping 8.8.8.8
traceroute example.com
```

### ss / netstat
```
ss -tulwn        # TCP/UDP sockets
netstat -tulnp   # older, similar
```

### tcpdump
```
tcpdump -i eth0 port 80
```

### nmap
```
nmap 192.168.1.1
```

---

## 5. File & Disk Diagnostics

### df / du
```
df -h            # disk usage per filesystem
du -sh *         # folder sizes
```

### smartctl
```
sudo smartctl -a /dev/sda  # check disk health
```

### fsck
```
sudo fsck /dev/sda1        # filesystem check
```

---

## 6. Debugging Programs

### gdb (GNU Debugger)
```
gdb ./myprogram
break main
run
next / step / continue
print var
backtrace
```

### strace
```
strace -p <pid>
strace -o output.log ./myprogram
```
- Trace syscalls and signals.

### ltrace
```
ltrace ./myprogram
```
- Trace library calls.

### valgrind
```
valgrind --leak-check=full ./myprogram
```
- Memory leaks, invalid memory access.

---

## 7. Debugging Scripts

### bash -x
```
bash -x script.sh
```
- Trace execution line by line.

### set -e / set -u
- `set -e` → exit on error
- `set -u` → treat unset variables as error

---

## 8. Profiling

### perf
```
perf top
perf record -g ./myprogram
perf report
```

### time
```
time ./script.sh
```
- Measure execution time.

---

## 9. Core Dumps
```
ulimit -c unlimited   # enable core dumps
./myprogram           # crash produces core
gdb ./myprogram core  # analyze core
```

---

## 10. Package / Dependency Issues
```
dpkg -l | grep <package>
ldd ./myprogram        # list dynamic libs
```

---

## 11. Useful Tips
- Always reproduce issue first.
- Start from high-level (system) down to low-level (code).
- Use logs + tracing before guessing.
- Keep copies of config before testing changes.
- Combine `watch` + commands for real-time diagnostics.
- For multi-threaded programs, gdb + `thread apply all bt` helps.
- For intermittent network issues, tcpdump + Wireshark pairing is best.

