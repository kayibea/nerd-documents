# Bash/Linux Package Management Cheat Sheet (Debian-focused)

## 1. APT Basics

| Command | Description |
|---------|-------------|
| `apt update` | Refresh package lists |
| `apt upgrade` | Upgrade all installed packages |
| `apt full-upgrade` | Upgrade with dependency changes/removals |
| `apt install <pkg>` | Install package |
| `apt remove <pkg>` | Remove but keep config files |
| `apt purge <pkg>` | Remove including config files |
| `apt autoremove` | Clean unused dependencies |
| `apt search <term>` | Search in repo |
| `apt show <pkg>` | Package details |

## 2. apt-get & apt-cache (Low-level)

| Command | Description |
|---------|-------------|
| `apt-get install <pkg>` | Traditional installer |
| `apt-get build-dep <pkg>` | Install build deps for source package |
| `apt-get source <pkg>` | Download source code |
| `apt-cache policy <pkg>` | Show installed vs available versions |
| `apt-cache depends <pkg>` | Dependencies |

## 3. dpkg (Direct Package Manipulation)

| Command | Description |
|---------|-------------|
| `dpkg -i file.deb` | Install local .deb |
| `dpkg -r <pkg>` | Remove |
| `dpkg -P <pkg>` | Purge |
| `dpkg -l` | List installed packages |
| `dpkg -L <pkg>` | Files installed by pkg |
| `dpkg -S <file>` | Find which package owns file |
| `dpkg --contents file.deb` | Show contents of .deb |

## 4. Snap

| Command | Description |
|---------|-------------|
| `snap find <pkg>` | Search snaps |
| `snap install <pkg>` | Install |
| `snap remove <pkg>` | Uninstall |
| `snap list` | Installed snaps |

## 5. Flatpak

| Command | Description |
|---------|-------------|
| `flatpak search <term>` | Search apps |
| `flatpak install flathub <pkg>` | Install from flathub |
| `flatpak uninstall <pkg>` | Remove |
| `flatpak update` | Update apps |
| `flatpak list` | Installed flatpaks |

## 6. Repositories & Sources

| Command | Description |
|---------|-------------|
| `nano /etc/apt/sources.list` | Main repo config |
| `/etc/apt/sources.list.d/` | Additional repo files |
| `apt-key` | Deprecated key management (use gpg + keyrings instead) |

### Adding a repo (modern way)
```
wget -O /usr/share/keyrings/repo-key.gpg https://example.com/key.gpg
echo "deb [signed-by=/usr/share/keyrings/repo-key.gpg] https://repo.example.com stable main" > /etc/apt/sources.list.d/example.list
apt update
```

## 7. Cleaning & Maintenance

| Command | Description |
|---------|-------------|
| `apt autoclean` | Remove cached obsolete packages |
| `apt clean` | Clear all local cache |
| `journalctl --vacuum-size=100M` | Reduce logs (not strictly package-related but useful) |

## 8. Inspecting Packages

| Command | Description |
|---------|-------------|
| `apt-file search <file>` | Find package providing file (must install apt-file) |
| `apt-file update` | Refresh apt-file cache |
| `apt depends <pkg>` | Show dependencies |
| `apt rdepends <pkg>` | Reverse dependencies |

## 9. Building Packages

| Command | Description |
|---------|-------------|
| `dpkg-buildpackage` | Build .deb packages |
| `debuild` | More complete build wrapper |
| `mk-build-deps` | Generate build deps package |

## 10. Tips & Tricks

- Use `apt policy` to see which version will be installed.
- Use `dpkg -S /usr/bin/foo` to find owning package.
- For local packages: `apt install ./file.deb` handles deps automatically vs `dpkg -i`.
- Prefer `apt purge` when removing stuff to avoid stale config.
- Use `apt-mark hold <pkg>` to freeze package version.
- Use `apt list --installed` for complete installed list.
- For debugging dependency issues: `apt --fix-broken install`.
- Check what will be removed: `apt autoremove --dry-run`.
