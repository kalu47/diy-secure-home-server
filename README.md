# 🛡️ DIY Secure Home Server Architecture Masterclass

```{=html}
<p align="center">
```
**Build a secure, modern, and scalable home server using Ubuntu Server
LTS**

Access your homelab securely from anywhere in the world **without
unnecessarily exposing your home network**.

Perfect for developers, homelab enthusiasts, gamers, makers, and
self-hosters.

```{=html}
</p>
```

------------------------------------------------------------------------

## 🚀 What You'll Build

A production-inspired home infrastructure using two complementary
technologies:

-   🌐 **Cloudflare Tunnel** → Securely publish web applications
-   ⚡ **Tailscale Mesh VPN** → Privately access infrastructure from
    anywhere

This architecture is ideal for:

-   🐳 Docker containers
-   🎮 Game servers
-   🎬 Media servers
-   🤖 Home automation
-   📊 Dashboards
-   🗄️ NAS storage
-   🧪 Development environments
-   🔧 Personal projects

------------------------------------------------------------------------

## 📑 Table of Contents

-   [Features](#-features)
-   [Security Notice](#-security-notice)
-   [Architecture Overview](#-architecture-overview)
-   [Prerequisites](#-prerequisites)
-   [Recommended Hardware](#-recommended-hardware)
-   [Method 1: Cloudflare Tunnel](#-method-1-cloudflare-tunnel)
-   [Method 2: Tailscale Mesh VPN](#-method-2-tailscale-mesh-vpn)
-   [Hosting Game Servers](#-hosting-game-servers)
-   [Security Best Practices](#-security-best-practices)
-   [Example Home Lab Setup](#-example-home-lab-setup)
-   [Final Recommendation](#-final-recommendation)

------------------------------------------------------------------------

## ✨ Features

  Feature            Cloudflare Tunnel   Tailscale
  ------------------ ------------------- -----------
  Web Dashboards     ✅                  ⚪
  Public Sharing     ✅                  ❌
  SSH Access         ⚪                  ✅
  NAS Access         ⚪                  ✅
  File Transfers     ⚪                  ✅
  Game Servers       ⚠️                  ✅
  Zero Trust         ✅                  ✅
  Public IP Hidden   ✅                  ✅

------------------------------------------------------------------------

## 🔒 Security Notice

**Never publish or share real infrastructure values.**

Replace sensitive information with placeholders.

### ❌ Never expose

-   Domain names
-   Public IP addresses
-   Internal IP addresses
-   Email addresses
-   SSH usernames
-   Hostnames
-   Cloudflare Tunnel IDs
-   Tailscale IP addresses
-   API keys
-   Tokens

### ✅ Use placeholders

``` text
yourdomain.com
dashboard.yourdomain.com
gameserver.yourdomain.com
username
192.168.1.100
100.x.x.x
```

> Treat your homelab like production infrastructure.

------------------------------------------------------------------------

## 🏗️ Architecture Overview

### 🌐 Cloudflare Tunnel

``` text
Internet
   │
   ▼
Cloudflare Edge
   │
   ▼
Cloudflare Tunnel
   │
   ▼
Ubuntu Server
```

### ⚡ Tailscale Mesh VPN

``` text
Phone
 │
Laptop
 │
Tablet
 │
└── Tailscale Mesh ──┘
          │
          ▼
   Ubuntu Server
```

------------------------------------------------------------------------

## 📋 Prerequisites

### Hardware

-   💻 Old PC, Mini PC, or Laptop
-   🌐 Stable Internet Connection
-   📱 Phone, Tablet, or Laptop

### Accounts

-   🌐 Cloudflare Account
-   ⚡ Tailscale Account

### Knowledge

-   🐧 Basic Linux Knowledge

------------------------------------------------------------------------

## 🧰 Recommended Hardware

### 💽 Multi-HDD Expansion

Perfect for:

-   NAS storage
-   Media libraries
-   Backups
-   Game servers

🔗 https://amzn.to/4vd6Jce

### 💾 2.5" HDD / SSD USB Adapter

Ideal for:

-   Laptop HDDs
-   SATA SSDs
-   Temporary backups

🔗 https://amzn.to/43CiHjC

### 💾 3.5" HDD USB Adapter

Ideal for:

-   Desktop HDDs
-   Large media collections
-   Permanent storage expansion

🔗 https://amzn.to/4xLKUCu

### 📌 Suggested Storage Layout

  Purpose             Recommended Drive
  ------------------- ---------------------
  Movies & TV         3.5" HDD
  Game Servers        Dedicated HDD
  Backups             2.5" HDD
  Docker Containers   SSD
  NAS Storage         Multi-HDD Enclosure

------------------------------------------------------------------------

# 🌐 Method 1: Cloudflare Tunnel

Cloudflare Tunnel creates a secure outbound connection between your
server and Cloudflare.

**No inbound ports required.**

## 1️⃣ Harden Ubuntu Firewall

``` bash
sudo apt update && sudo apt upgrade -y

sudo ufw default deny incoming
sudo ufw default allow outgoing

sudo ufw allow from 192.168.1.0/24 to any port 22

sudo ufw enable

sudo ufw status verbose
```

## 2️⃣ Install Cloudflare Tunnel

``` bash
curl -L -o cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb

sudo dpkg -i cloudflared.deb

cloudflared --version
```

## 3️⃣ Authenticate

``` bash
cloudflared tunnel login
```

## 4️⃣ Create Tunnel

``` bash
cloudflared tunnel create my-server
```

## 5️⃣ Create DNS Route

``` bash
cloudflared tunnel route dns my-server dashboard.yourdomain.com
```

## 6️⃣ Run Tunnel

``` bash
cloudflared tunnel run my-server
```

## 7️⃣ Configure Zero Trust

``` text
Cloudflare Dashboard
 ↓
Zero Trust
 ↓
Access
 ↓
Applications
```

Authentication:

-   One-Time Passcode (OTP)
-   Restrict approved email addresses

------------------------------------------------------------------------

# ⚡ Method 2: Tailscale Mesh VPN

Tailscale creates a secure WireGuard mesh network.

**Nothing is exposed to the public internet.**

## Install

``` bash
curl -fsSL https://tailscale.com/install.sh | sh
```

## Authenticate

``` bash
sudo tailscale up
```

## Get Tailscale IP

``` bash
tailscale ip -4
```

Example:

``` text
100.x.x.x
```

## SSH

``` bash
ssh username@100.x.x.x
```

## Dashboard

``` text
http://100.x.x.x:3000
```

## File Transfer

``` bash
scp file.zip username@100.x.x.x:/home/username/
```

------------------------------------------------------------------------

## 🎮 Hosting Game Servers

  Service              Recommendation
  -------------------- -----------------------
  Palworld             ⚡ Tailscale
  Minecraft            ⚡ Tailscale
  Small Friend Group   ⚡ Tailscale
  Public Community     🌐 Port Forward + DNS

### Palworld

Default Port:

``` text
UDP 8211
```

> Cloudflare Tunnel does not proxy Palworld gameplay traffic.

Use:

``` text
gameserver.yourdomain.com
```

as a **DNS-only** record.

------------------------------------------------------------------------

## 🔐 Security Best Practices

### SSH Keys

``` bash
ssh-keygen -t ed25519
```

Edit:

``` bash
sudo nano /etc/ssh/sshd_config
```

Set:

``` text
PasswordAuthentication no
PermitRootLogin no
```

Restart:

``` bash
sudo systemctl restart ssh
```

### Automatic Updates

``` bash
sudo apt install unattended-upgrades

sudo dpkg-reconfigure unattended-upgrades
```

### Dedicated Service Accounts

``` bash
sudo adduser gameserver
```

Avoid running services as root.

### Backup Critical Data

``` text
/home/
/etc/
/docker/
/server/
```

------------------------------------------------------------------------

## 🏡 Example Home Lab Setup

``` text
Ubuntu Server
│
├── Cloudflare Tunnel
│   ├── dashboard.yourdomain.com
│   ├── media.yourdomain.com
│   └── containers.yourdomain.com
│
├── Tailscale
│   ├── SSH
│   ├── NAS
│   ├── File Transfers
│   └── Private Services
│
└── Docker Containers
    ├── Game Server
    ├── Jellyfin
    ├── Portainer
    └── Development Tools
```

------------------------------------------------------------------------

# 🎯 Final Recommendation

**Use both technologies together.**

### 🌐 Cloudflare Tunnel

Best for:

-   Jellyfin
-   Portainer
-   Home Assistant
-   Dashboards
-   Internal websites

### ⚡ Tailscale

Best for:

-   SSH
-   NAS
-   Development environments
-   File transfers
-   Game servers

Benefits:

-   🔒 Security
-   ⚡ Performance
-   🌍 Remote access
-   🏡 Home network privacy

Your public IP remains hidden while your infrastructure stays accessible
from anywhere.

------------------------------------------------------------------------

## 👥 Built For

-   👨‍💻 Developers
-   🏡 Homelab Enthusiasts
-   🎮 Gamers
-   🤖 IoT Makers
-   🖨️ 3D Printing Enthusiasts
-   🚁 Drone Enthusiasts

------------------------------------------------------------------------

## ❤️ Support

Some links are affiliate links.

Purchasing through them helps support future tutorials, projects, and
homelab experiments at **no additional cost to you**.

Thank you for your support.

------------------------------------------------------------------------

# 🚀 Happy Self-Hosting!
