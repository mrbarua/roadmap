That error —

```
ping: google.com: Name or service not known
```

— means your system **can’t resolve DNS names** (i.e., it can’t translate `google.com` into an IP address).

Let’s troubleshoot step by step:

---

### 🔍 1. Check Internet Connectivity

Try pinging a public IP directly:

```bash
ping -c 4 8.8.8.8
```

* If this **works**, you have internet access but **DNS is broken**.
* If this **fails**, you might have **no network connection** or **routing issues**.

---

### 🧭 2. Check DNS Configuration

See what DNS servers your system is using:

```bash
cat /etc/resolv.conf
```

You should see something like:

```
nameserver 8.8.8.8
nameserver 1.1.1.1
```

If it’s empty or has invalid entries, edit it:

```bash
sudo nano /etc/resolv.conf
```

and add:

```
nameserver 8.8.8.8
nameserver 1.1.1.1
```

> ⚠️ Note: On Rocky Linux, `NetworkManager` may overwrite this file automatically. To make it persistent, use:

```bash
nmcli con mod "System eth0" ipv4.dns "8.8.8.8 1.1.1.1"
nmcli con up "System eth0"
```

(Replace `"System eth0"` with your actual connection name from `nmcli con show`.)

---

### 🧱 3. Check NetworkManager or Systemd-Resolved

If DNS still doesn’t work:

```bash
systemctl status NetworkManager
systemctl status systemd-resolved
```

Make sure they’re **running and active**.

If not, restart:

```bash
sudo systemctl restart NetworkManager
```

---

### 🧰 4. Check `/etc/hosts`

Ensure `localhost` and other basics are defined properly:

```
127.0.0.1   localhost
::1         localhost
```

---

### 🧩 5. If You’re Using a Virtual Machine

If this is a Rocky Linux VM (e.g., on VMware, VirtualBox, or a cloud instance):

* Make sure the network adapter is set to **NAT** or **Bridged**.
* Reboot or run:

  ```bash
  sudo dhclient
  ```

---

Would you like to show me the output of:

```bash
ping -c 4 8.8.8.8
cat /etc/resolv.conf
nmcli con show
```

?
Then I can tell you exactly where the issue lies.


