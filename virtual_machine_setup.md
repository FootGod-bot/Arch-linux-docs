# Virtual Machine Setup (Arch Linux + Proxmox)

## 1. QEMU Guest Agent

Install the QEMU guest agent (used by Proxmox for better VM integration):

```bash
pacman -Syu qemu-guest-agent
```

Then enable it in Proxmox VM settings.

---

## 2. Cloud-Init Setup

Cloud-Init lets Proxmox auto-configure things like IPs, users, SSH keys, etc.

---

### 2.1 Install Cloud-Init

```bash
pacman -S cloud-init
```

---

### 2.2 Enable Required Services

Enable all Cloud-Init services:

```bash
systemctl enable cloud-init-local.service
systemctl enable cloud-init.service
systemctl enable cloud-config.service
systemctl enable cloud-final.service
```

---

### 2.3 Configure `/etc/cloud/cloud.cfg`

Edit the config file:

```bash
nano /etc/cloud/cloud.cfg
```

Add this line:

```yaml
datasource_list: [ NoCloud, ConfigDrive ]
```

Then ensure system config includes:

```yaml
system_info:
  network:
    renderers: ['network-manager']
```

---

### 2.4 Clean Machine State (important for cloning)

Run this before turning it into a template:

```bash
cloud-init clean --logs

truncate -s 0 /etc/machine-id
rm -f /var/lib/dbus/machine-id
ln -s /etc/machine-id /var/lib/dbus/machine-id
```

---

### 2.5 Power Off VM

```bash
poweroff
```

---

### 2.6 Add Cloud-Init Drive in Proxmox

In Proxmox:

* Add **Cloud-Init Drive** to the VM
* (Optional) Set a **new static IP** to confirm Cloud-Init is working

---

### 2.7 Boot & Verify

Start the VM again.

If things like:

* IP address changes
* user config applies
* SSH keys update

…then Cloud-Init is working correctly.
