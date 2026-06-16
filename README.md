# diy-secure-home-server

# 🛡️ Secure Home Server Architecture (Invisible to the Public)

Welcome to the documentation for my secure home server setup! This guide explains how to host a server from your home network with **zero open router ports**, making it practically invisible to public IP scanners. 

This repository serves as the companion guide to my video tutorial.

## ✨ Key Features
*   **100% Inbound Traffic Blocked:** No port forwarding required on the router.
*   **Outbound Tunnels:** Utilizes Cloudflare Tunnels (`cloudflared`) to establish an inside-out connection to the web.
*   **Identity-Based Authentication:** Protected by Cloudflare Zero Trust (OTP/Email verification) before anyone can even see the login screen.
*   **Local Hardening:** UFW (Uncomplicated Firewall) configured to block all local traffic except for explicit management devices.

## 🛠️ Prerequisites
*   An old PC or dedicated hardware.
*   **OS:** Ubuntu 26.04 LTS (Server Edition).
*   A custom domain name mapped to a Cloudflare account.
*   Cloudflare Zero Trust enabled on your account (Free tier).

## 🚀 Installation & Setup Guide

### 1. Hardening the Local Firewall (UFW)
Before connecting to the internet, lock down the local machine.
\`\`\`bash
sudo apt update && sudo apt upgrade -y
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow from <YOUR_LOCAL_IP> to any port 22
sudo ufw enable
\`\`\`

### 2. Installing Cloudflare Tunnel
Instead of exposing your IP, install the Cloudflare daemon to route traffic outbound.
\`\`\`bash
# Download and install cloudflared
curl -L --output cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared.deb

# Authenticate with your Cloudflare account
cloudflared tunnel login
\`\`\`

### 3. Creating and Routing the Tunnel
\`\`\`bash
# Create the tunnel (replace 'my-server' with your preferred name)
cloudflared tunnel create my-server

# Route your domain to the tunnel
cloudflared tunnel route dns my-server secure.yourdomain.com

# Run the tunnel
cloudflared tunnel run my-server
\`\`\`

## 🔒 Zero Trust Configuration
1. Navigate to the Cloudflare Zero Trust Dashboard.
2. Go to **Access > Applications** and add your subdomain (`secure.yourdomain.com`).
3. Set a policy to require **Email Authentication**.
4. Whitelist only your specific email address. 

Now, anyone hitting your domain will be met with a Cloudflare login screen. If they don't have your email, the connection is dropped.

---
*Created for the tech and homelab community.*