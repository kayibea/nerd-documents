# systemd Timers

systemd timers are a modern replacement for cron jobs on systemd-based
Linux systems. They integrate tightly with services, logging,
dependencies, and boot handling.

------------------------------------------------------------------------

## Why systemd Timers?

Compared to cron, systemd timers offer:

-   Better error handling
-   Integrated logging via `journalctl`
-   Dependency management
-   Missed-run handling (catch-up after downtime)
-   Per-user timers
-   Clear separation of *what runs* vs *when it runs*

------------------------------------------------------------------------

## Basic Concepts

A systemd timer always works with a **service**:

-   `example.service` → defines *what* runs
-   `example.timer` → defines *when* it runs

Both files usually live in:

    /etc/systemd/system/

------------------------------------------------------------------------

## Example: Daily Backup

### 1. Service Unit

`/etc/systemd/system/backup.service`

``` ini
[Unit]
Description=Daily backup job

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup.sh
```

### 2. Timer Unit

`/etc/systemd/system/backup.timer`

``` ini
[Unit]
Description=Run backup daily

[Timer]
OnCalendar=daily
Persistent=true

[Install]
WantedBy=timers.target
```

### 3. Enable the Timer

``` bash
sudo systemctl daemon-reexec
sudo systemctl enable --now backup.timer
```

------------------------------------------------------------------------

## OnCalendar Syntax

Examples:

  Expression             Meaning
  ---------------------- --------------------------
  `daily`                Once per day
  `weekly`               Once per week
  `Mon *-*-* 03:00:00`   Every Monday at 03:00
  `*-*-01 00:00:00`      First day of every month
  `*-*-* *:00:00`        Every hour

Test expressions:

``` bash
systemd-analyze calendar "Mon *-*-* 03:00"
```

------------------------------------------------------------------------

## Persistent Timers

    Persistent=true

If the system was powered off, the job runs once at next boot.

This is one of the biggest advantages over cron.

------------------------------------------------------------------------

## Monitoring Timers

List timers:

``` bash
systemctl list-timers
```

Check logs:

``` bash
journalctl -u backup.service
```

------------------------------------------------------------------------

## User Timers

Timers can run without root:

``` bash
~/.config/systemd/user/
```

Enable with:

``` bash
systemctl --user enable --now example.timer
```

Enable lingering for boot-time execution:

``` bash
loginctl enable-linger username
```

------------------------------------------------------------------------

## systemd Timers vs Cron

  Feature          cron          systemd
  ---------------- ------------- ------------
  Logging          Mail/stdout   journalctl
  Dependencies     ❌            ✅
  Catch-up runs    ❌            ✅
  User timers      Limited       Full
  Syntax clarity   Medium        High

------------------------------------------------------------------------

## When to Use Cron Instead

-   Very old systems without systemd
-   Minimal containers
-   Compatibility requirements

Otherwise, **prefer systemd timers**.

------------------------------------------------------------------------

## References

-   `man systemd.timer`
-   `man systemd.service`
-   `man systemd.time`
