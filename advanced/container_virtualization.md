# Container & Virtualization Tools Cheat Sheet

## 1. Docker (Containers)

### Basic Commands
```
docker ps                     # list running containers
docker ps -a                  # list all containers
docker images                 # list images
```

### Running Containers
```
docker run -it ubuntu bash    # interactive shell
docker run -d nginx           # detached
```

### Managing
```
docker stop <id>
docker start <id>
docker rm <id>
docker rmi <image>
```

### Exec Into Container
```
docker exec -it <id> bash
```

### Build Images
```
docker build -t myimg .
```

---

## 2. Podman (Rootless Containers)
Docker-compatible but secure.
```
podman ps
podman run -it ubuntu bash
podman build -t myimg .
```

---

## 3. Docker Compose
Multi-container setups.
```
docker compose up -d
docker compose down
```

Example `docker-compose.yml`:
```
services:
  web:
    image: nginx
    ports:
      - "80:80"
```

---

## 4. LXC / LXD (System Containers)
Lightweight OS-level virtualization.
```
lxc launch images:debian/12 myvm
lxc list
lxc exec myvm -- bash
```

---

## 5. systemd-nspawn (Lightweight Sandbox)
```
sudo systemd-nspawn -D /srv/container1
```

Create container rootfs using debootstrap.

---

## 6. QEMU/KVM (Full Virtualization)

### Check if virtualization supported
```
lscpu | grep VT
```

### Create & run VMs
```
qemu-img create -f qcow2 vm.qcow2 20G
qemu-system-x86_64 -enable-kvm -m 4G -drive file=vm.qcow2,format=qcow2
```

---

## 7. virt-manager (GUI for KVM/QEMU)
```
sudo apt install virt-manager
```
Manage VMs easily using GUI.

---

## 8. QEMU Tools
```
qemu-img info vm.qcow2
qemu-img convert -O qcow2 old.img new.qcow2
```

---

## 9. Vagrant (VM Automation)
```
vagrant init debian/bookworm64
vagrant up
vagrant ssh
vagrant destroy
```

---

## 10. Firecracker / Kata (MicroVMs)
For extremely lightweight VMs.

Firecracker example:
- Used by AWS Lambda
- Requires kernel + rootfs manually

---

## 11. Kubernetes (Orchestrator)
Overkill for home use but widely used.
```
kubectl get pods
kubectl logs <pod>
```

MiniKube to test locally:
```
minikube start
```

---

## 12. libvirt CLI
```
virsh list --all
virsh start myvm
virsh shutdown myvm
```

---

## 13. Best Use Cases
| Tool | Use Case |
|------|----------|
| **Docker** | Apps, microservices |
| **Podman** | Rootless secure containers |
| **LXC/LXD** | Lightweight Linux VMs |
| **QEMU/KVM** | Full OS virtualization |
| **virt-manager** | GUI VM workflow |
| **Vagrant** | Reproducible dev VMs |
| **Kubernetes** | Large-scale orchestration |

---

## 14. Useful Tips
- Use `podman` instead of Docker for rootless security.
- Use qcow2 for VMs; supports snapshots + compression.
- Use `docker system prune` to clean junk.
- Distrobox can integrate containers with desktop.

