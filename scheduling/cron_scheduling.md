# Bash/Linux Cron & Scheduling Cheat Sheet

## 1. Cron Basics

| Command | Description |
|---------|-------------|
| `crontab -e` | Edit current user's crontab |
| `crontab -l` | List current user's cron jobs |
| `crontab -r` | Remove entire crontab (careful) |
| `sudo crontab -e -u <user>` | Edit another user's crontab |
| `/etc/crontab` | System-wide cron file |
| `/etc/cron.d/` | Directory for package/service cron files |

## 2. Cron Format (5 Fields)
```
┌───────────── minute (0–59)
│ ┌───────────── hour (0–23)
│ │ ┌───────────── day of month (1–31)
│ │ │ ┌───────────── month (1–12)
│ │ │ │ ┌───────────── day of week (0–7) (0 or 7 = Sunday)
* * * * * command-to-run
```

### Examples
| Schedule | Meaning |
|----------|---------|
| `0 * * * *` | Every hour |
| `0 0 * * *` | Every day at midnight |
| `*/5 * * * *` | Every 5 minutes |
| `0 18 * * 1-5` | Weekdays at 18:00 |
| `30 2 1 * *` | Monthly backup at 02:30 on the 1st |

## 3. Cron Environment Notes
- PATH is limited; use full paths `/usr/bin/rsync`.
- No shell aliases.
- Output is mailed to user unless redirected.
- Use `SHELL=/bin/bash` at top to enforce bash.

### Redirecting Output
```
* * * * * /path/script.sh >> /var/log/myscript.log 2>&1
```

## 4. anacron (Run missed jobs)
Used for laptops or systems not always on.

| File | Purpose |
|------|---------|
| `/etc/anacrontab` | Defines daily/weekly/monthly jobs |

### anacrontab format
```
period   delay   job-id   command
```
Example:
```
1 5 cron.daily /usr/bin/run-parts /etc/cron.daily
```

## 5. at (One-Time Scheduling)

| Command | Description |
|---------|-------------|
| `echo "shutdown -r now" | at 02:00` | Run command at 2 AM |
| `at -l` | List pending jobs |
| `at -r <job>` | Remove job |

### Natural language supported
```
at now + 10 minutes
at midnight\ nat 3pm tomorrow
```

## 6. systemd Timers (Modern Alternative to Cron)
More reliable, supports logging & dependencies.

### Create a timer
```
/etc/systemd/system/myjob.service
/etc/systemd/system/myjob.timer
```

### Example service
```
[Unit]
Description=My script

[Service]
Type=oneshot
ExecStart=/usr/local/bin/myscript.sh
```

### Example timer
```
[Unit]
Description=Run script every 15 minutes

[Timer]
OnBootSec=5min
OnUnitActiveSec=15min
Unit=myjob.service

[Install]
WantedBy=timers.target
```
Enable it:
```
systemctl enable --now myjob.timer
```

### List timers
```
systemctl list-timers
```

## 7. Common Cron Mistakes & Fixes

- Forgetting full paths → cron cannot find command.
- Missing executable bit on scripts → `chmod +x script.sh`.
- Using `~` for home path → cron does not expand it.
- Script relies on env vars not set → define them inside script.
- Output not visible → redirect logs explicitly.

## 8. Tips & Tricks

- Test cron expressions using online generators.
- Put scripts in `/usr/local/bin` or `/opt/scripts/` for clarity.
- For debugging: add `set -x` at top of script.
- Use systemd timers for anything critical or complex.
- Check cron logs: `journalctl -u cron` or `/var/log/syslog` on Debian.
