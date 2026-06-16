# 🛡️ DIY Secure Home Server Architecture Masterclass

Welcome to the ultimate step-by-step guide for setting up a highly secure, modern home server using **Ubuntu Server LTS**.

This documentation covers **two different methods** to securely access your home lab from anywhere in the world **without exposing your home network or opening unnecessary ports on your router**.

---

# 🛠️ Choose Your Architecture

Select the approach that best suits your needs.

| Feature | 🌐 Cloudflare Tunnel | ⚡ Tailscale Mesh VPN |
|---------|---------------------|---------------------|
| **Primary Use Case** | Web applications, dashboards, remote access via custom domains | Private infrastructure, gaming servers, media transfers |
| **Visibility** | Public gateway protected by Zero Trust | Completely private and invisible to the public internet |
| **Protocols** | HTTP / HTTPS | TCP, UDP, SSH, SMB, RDP, and all native protocols |
| **Performance** | Proxied via Cloudflare edge network | Direct peer-to-peer (P2P) |
| **Best For** | Sharing services securely with others | Personal infrastructure and private networks |

---

# 📋 Prerequisites

You'll need:

- An old PC, mini-PC, or laptop
- Ubuntu Server LTS installed
- Stable internet connection
- A Cloudflare account (Method 1)
- A Tailscale account (Method 2)
- A domain name (optional but recommended)
- Basic Linux terminal knowledge

---

# 🌐 Method 1: Cloudflare Tunnel (Secure Public Access)

Cloudflare Tunnel acts as a secure gateway between your server and the internet.

Your server establishes an outbound connection to Cloudflare, meaning:

- No inbound ports need to be opened.
- Your public IP stays hidden.
- Access can be protected using Zero Trust policies.

## Architecture

```text
Internet
   ↓
Cloudflare Edge Network
   ↓
Cloudflare Tunnel
   ↓
Ubuntu Server
```

---

## 1️⃣ Harden Ubuntu Firewall (UFW)

Update packages:

```bash
sudo apt update && sudo apt upgrade -y
```

Set default rules:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

Allow SSH only from your local network:

```bash
sudo ufw allow from <YOUR_LOCAL_IP> to any port 22
```

Enable firewall:

```bash
sudo ufw enable
```

Verify:

```bash
sudo ufw status verbose
```

---

## 2️⃣ Install Cloudflare Tunnel

Download:

```bash
curl -L \
-o cloudflared.deb \
https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
```

Install:

```bash
sudo dpkg -i cloudflared.deb
```

Verify:

```bash
cloudflared --version
```

---

## 3️⃣ Authenticate Cloudflare

Login:

```bash
cloudflared tunnel login
```

A browser window will open.

Select your domain.

---

## 4️⃣ Create a Tunnel

Create the tunnel:

```bash
cloudflared tunnel create my-server
```

---

## 5️⃣ Create a DNS Route

```bash
cloudflared tunnel route dns my-server secure.yourdomain.com
```

Example:

```text
secure.kalu47.com
```

---

## 6️⃣ Run the Tunnel

```bash
cloudflared tunnel run my-server
```

---

## 7️⃣ Configure Cloudflare Zero Trust

Navigate to:

```text
Cloudflare Dashboard
↓
Zero Trust
↓
Access
↓
Applications
```

Create an application.

Configure:

```text
Application Type:
Self-hosted

Domain:
secure.yourdomain.com
```

Create an access policy:

```text
Authentication:
One-Time Passcode (OTP)

Allowed Emails:
your@email.com
```

Now only approved users can access the service.

---

# ⚡ Method 2: Tailscale Mesh VPN (Private Infrastructure)

Tailscale creates a secure peer-to-peer network using WireGuard.

Nothing is exposed publicly.

## Architecture

```text
Phone
   │
Laptop
   │
Tablet
   │
└─── Tailscale Mesh Network ───┘
              │
              │
       Ubuntu Server
```

---

## 1️⃣ Install Tailscale

Run:

```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

---

## 2️⃣ Authenticate the Server

Start Tailscale:

```bash
sudo tailscale up
```

A login URL will appear.

Open it in your browser.

Sign in.

---

## 3️⃣ Install Tailscale on Your Devices

Install Tailscale on:

- Phone
- Tablet
- Laptop
- Desktop PC

Log in using the same account.

Enable the VPN.

---

## 4️⃣ Find Your Tailscale IP

```bash
tailscale ip -4
```

Example:

```text
100.115.22.47
```

---

## 5️⃣ Connect Securely

SSH:

```bash
ssh username@100.115.22.47
```

Access a web dashboard:

```text
http://100.115.22.47:3000
```

Transfer media:

```bash
scp movie.mkv username@100.115.22.47:/home/username/
```

Everything stays inside your private mesh network.

---

# 🔐 Security Recommendations

Always follow these best practices:

### ✅ Use SSH Keys

Generate:

```bash
ssh-keygen -t ed25519
```

Disable password authentication:

```bash
sudo nano /etc/ssh/sshd_config
```

Set:

```ini
PasswordAuthentication no
PermitRootLogin no
```

Restart SSH:

```bash
sudo systemctl restart ssh
```

---

### ✅ Keep Ubuntu Updated

```bash
sudo apt update
sudo apt upgrade -y
```

---

### ✅ Enable Automatic Security Updates

Install:

```bash
sudo apt install unattended-upgrades
```

Enable:

```bash
sudo dpkg-reconfigure unattended-upgrades
```

---

### ✅ Use Dedicated Service Accounts

Do not run applications as root.

Example:

```bash
sudo adduser palworld
```

---

### ✅ Backup Important Data

Backup regularly:

```text
/home/
/etc/
/docker/
/server/
```

---

# 🎮 Suggested Usage

| Service | Recommended Method |
|---------|------------------|
| SSH | Tailscale |
| Jellyfin | Cloudflare Tunnel |
| Portainer | Cloudflare Tunnel |
| Home Assistant | Cloudflare Tunnel |
| Palworld Server | Tailscale (Private) |
| NAS | Tailscale |
| Development Environment | Tailscale |
| IoT Dashboard | Cloudflare Tunnel |

---

# 🏡 Example Home Lab Setup

```text
Ubuntu Server
│
├── Cloudflare Tunnel
│   ├── watchdog.kalu47.com
│   ├── jellyfin.kalu47.com
│   └── portainer.kalu47.com
│
├── Tailscale
│   ├── SSH
│   ├── NAS
│   ├── File Transfers
│   └── Private Services
│
└── Docker Containers
    ├── Palworld
    ├── Jellyfin
    ├── Portainer
    └── Development Tools
```

---

# 🎯 Final Recommendation

### Use both technologies together.

**Cloudflare Tunnel**

→ Public-facing services

**Tailscale**

→ Private infrastructure and gaming

This provides the best balance between:

- 🔒 Security
- ⚡ Performance
- 🌍 Remote Accessibility
- 🏡 Home Network Privacy

Your home IP remains protected while your services stay accessible from anywhere in the world.

---

Built for homelab enthusiasts, developers, gamers, and makers. 🚀