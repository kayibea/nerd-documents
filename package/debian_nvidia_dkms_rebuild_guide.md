# Rebuilding NVIDIA Kernel Modules on Debian (DKMS Guide)

This guide explains how to rebuild NVIDIA kernel modules after a kernel
update on **Debian** systems using the **official Debian-packaged NVIDIA
drivers (DKMS)**.

If you installed NVIDIA drivers using the `.run` installer from NVIDIA's
website, scroll down to the **Unsupported `.run` Installer** section ---
but be aware that method is fragile and not recommended on Debian.

------------------------------------------------------------------------

## 1. Check Your Current Kernel

``` bash
uname -r
```

If you have not rebooted yet, list installed kernels:

``` bash
ls /lib/modules
```

------------------------------------------------------------------------

## 2. Install Matching Kernel Headers

DKMS **requires kernel headers** for the kernel you're building against.

For the currently running kernel:

``` bash
sudo apt install linux-headers-$(uname -r)
```

For a new kernel you haven't booted yet:

``` bash
sudo apt install linux-headers-<kernel-version>
```

Example:

``` bash
sudo apt install linux-headers-6.7.12-amd64
```

------------------------------------------------------------------------

## 3. Rebuild NVIDIA Modules with DKMS

In most cases, this single command is enough:

``` bash
sudo dkms autoinstall
```

### Explicit rebuild (if needed)

``` bash
sudo dkms install nvidia/$(modinfo -F version nvidia)
```

------------------------------------------------------------------------

## 4. Regenerate initramfs

This is often triggered automatically, but do it manually to avoid
surprises:

``` bash
sudo update-initramfs -u -k all
```

------------------------------------------------------------------------

## 5. Reboot

``` bash
sudo reboot
```

After reboot, verify the driver:

``` bash
nvidia-smi
```

If this works, the module is correctly built and loaded.

------------------------------------------------------------------------

## 6. If DKMS Fails to Trigger Automatically

Force a clean rebuild by reinstalling the driver package:

``` bash
sudo apt install --reinstall nvidia-driver
```

This will: - Re-register DKMS modules - Rebuild against all installed
kernels - Regenerate initramfs

------------------------------------------------------------------------

## 7. Debugging & Sanity Checks

### Kernel version

``` bash
uname -r
```

### DKMS status

``` bash
dkms status
```

### Check loaded modules

``` bash
lsmod | grep nvidia
```

### Boot-time errors

``` bash
journalctl -b | grep -i nvidia
```

------------------------------------------------------------------------

## 8. Unsupported NVIDIA `.run` Installer (Not Recommended)

If you used NVIDIA's `.run` installer, you **must rebuild after every
kernel update**.

### Steps:

1.  Switch to TTY and stop X11

``` bash
sudo systemctl isolate multi-user.target
```

2.  Run the installer again

``` bash
sudo ./NVIDIA-Linux-*.run
```

3.  Reboot

``` bash
sudo reboot
```

### Why this sucks

-   No DKMS integration
-   Breaks on kernel updates
-   Conflicts with Debian packages
-   Pain with Secure Boot

If this is your setup, purge it and switch to Debian's packaged driver.

------------------------------------------------------------------------

## 9. Recommended NVIDIA Packages on Debian

``` bash
sudo apt install nvidia-driver nvidia-kernel-dkms
```

Optional utilities:

``` bash
sudo apt install nvidia-settings nvidia-smi
```

------------------------------------------------------------------------

## Summary

**Correct workflow after kernel update:**

1.  Install kernel headers
2.  Run `dkms autoinstall`
3.  Update initramfs
4.  Reboot
5.  Verify with `nvidia-smi`

That's it.

------------------------------------------------------------------------

*This guide assumes a standard Debian system using DKMS and packaged
NVIDIA drivers.*
