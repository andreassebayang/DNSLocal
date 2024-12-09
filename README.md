# DNSLocal
Membuat DNS Resolver dan Ad Blocking local

## Pi-Hole + Unbound
- Pi-Hole sebgai DNS sinkhole dan ad blocker
- Unbound sebagai recursive DNS resolver

### 1. **Persiapan**
```bash
sudo  apt update && apt upgrade -y
sudo apt install curl -y
```
### 2. **Instalasi Pi-Hole**
```bash
curl -sSL https://install.pi-hole.net | bash
```

### **Konfigurasi Installation Pi-Hole**
- Pilih interface network yang tersedia
- Pilih upstream DNS provider (Temporary)
- Pilih blocklist default
- Install/Enable web admin interface
- Catat password admin yang digenerate

### 3. **Instalasi Unbound**
```bash
sudo apt install unbound -y
```

### 4. **Buat konfigurasi Unbound**

```bash
sudo nano /etc/unbound/unbound.conf.d/pi-hole.conf

sudo systemctl restart unbound
```
>**Note:** Silahkan check file konfigurasi `pi-hole.conf` yang sudah disiapkan terpisah.

### 5. **Konfigurasi DoH dengan Cloudflared**
### **Install cloudflared**
```bash
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb

dpkg -i cloudflared-linux-amd64.deb
```
### **Buat Service Configuration**
```bash
sudo nano /etc/defaul/cloudflared
```
>**Note:** Silahkan check file konfigurasi  `cloudflared` yang sudah disiapkan terpisah.

### **Buat systemd service**
```bash
sudo nano /etc/systemd/system/cloudflared.service
```
>**Note:** Silahkan check file konfigurasi `cloudflared.service` yang sudah disiapkan terpisah.

### **Persiapkan user *cloudflared* sesuai pada konfigurasi**
```bash
sudo useradd -r -s /usr/sbin/nolodin cloudflared
sudo chown -R cloudflared:cloudflared /etc/default/cloudflared
```

### **Start dan Ennabled Service**
```bash
sudo systemctl daemon-reload
sudo systemctl enable cloudflared
sudo systemctl start cloudflared
```

### 6. **Testing**
### **Test Recursive DNS Resolutions**
```bash
dig google.com @127.0.0.1 -p 5335
```

### **Test DoH**
```bash
dig google.com @127.0.0.1 -p 5053
```

### **Test Pi-Hole**
```bash
dig google.com @IP_Server_PiHole
```

### 7. **Monitoring dan Maintenance**
- Monitor melalui Pi-Hole Dashboard
- Update regularly:
  ```bash
  pihole -up
  sudo apt update && apt upgrade -y
  ```
- Backup configuration
  ```bash
  pihole -a -t
  ```
  >**Note:** Backup telporter file

### 8. **Ganti Password Pi-Hole**
```bash
pihole -a -p
```
>**Note:** Ketik password yang diinginkan dan ketik ulang untuk konfirmasi
