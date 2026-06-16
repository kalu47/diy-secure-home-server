# 🛡️ DIY Secure Home Server Architecture Masterclass

Welcome to the ultimate step-by-step guide for setting up a highly secure, modern home server using **Ubuntu Server LTS**. 

This documentation covers two completely different methods to securely access your home laboratory from anywhere in the world without exposing your home network or opening a single port on your router.

---

## 🛠️ Choose Your Architecture

Depending on your use case, select the method that fits your needs:

| Feature | Method 1: Cloudflare Tunnels 🌐 | Method 2: Tailscale Mesh VPN ⚡ |
| :--- | :--- | :--- |
| **Primary Use Case** | Web applications, sharing dashboards with friends, public-facing links. | Private vaults, moving heavy gameplay/media files, gaming servers. |
| **Visibility** | **Public Gateway** (Protected behind a Zero Trust ID bouncer). | **100% Invisible** (Hidden from the public internet entirely). |
| **Protocols** | Best for HTTP/HTTPS web traffic. | Supports ALL protocols natively (TCP, UDP, SSH, SMB, etc.). |
| **Speed** | Proxy-based (Traffic routes through Cloudflare data centers). | Direct Peer-to-Peer (P2P), minimizing latency and routing limits. |

---

## 📋 General Prerequisites
*   An old PC, laptop, or mini-PC running **Ubuntu Server CLI**.
*   A client device (Phone, Tablet, or Laptop) to access the server remotely.
*   A basic understanding of the terminal.

---

## 🌐 Method 1: Cloudflare Tunnels (Public Web App Access)

This method acts as a public bouncer. It connects your server to a custom domain name so you can access web services, but instantly drops anyone trying to access it without passing your explicit email verification wall.

### 1. Hardening the Local Firewall (UFW)
Lock down the machine locally before establishing external connections.
```bash
sudo apt update && sudo apt upgrade -y
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow from <YOUR_LOCAL_IP> to any port 22
sudo ufw enable

### 2. Installing the Cloudflare Daemon

# Download and install cloudflared
curl -L --output cloudflared.deb [https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb](https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb)
sudo dpkg -i cloudflared.deb

# Authenticate with your Cloudflare account
cloudflared tunnel login

### 3. Creating and Routing the Tunnel

# Create your tunnel
cloudflared tunnel create my-server

# Route your sub-domain to the tunnel
cloudflared tunnel route dns my-server secure.yourdomain.com

# Run the tunnel daemon
cloudflared tunnel run my-server

### 4. Zero Trust Configuration

