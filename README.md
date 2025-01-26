# Azure VM Setup and Development Environment Guide

## Prerequisites
- Azure account
- SSH client
- VNC viewer (for GUI access)

## Create VM on Azure

1. **VM Configuration**
   - Select region (US East recommended for cost-effectiveness)
   - Choose Ubuntu as the image
   - Select VM size (8GB RAM recommended)
   - Save SSH key securely

## Initial VM Setup

### Connect to VM
```bash
ssh -i "path_to_key" username@ipaddress
```

### Update System
```bash
sudo apt update
sudo apt upgrade
sudo apt install curl git build-essential
```

## GUI Setup (Optional)

### Install XFCE Desktop Environment
```bash
# Install XFCE and related packages
sudo apt install xfce4 xfce4-goodies

# Install Display Manager
sudo apt install lightdm
sudo systemctl enable lightdm
sudo systemctl start lightdm

# Install TightVNC
sudo apt install tightvncserver
vncpasswd  # Set VNC password
```

### Configure VNC Server
```bash
# Kill existing VNC server (if any)
vncserver -kill :1

# Create/Edit VNC startup configuration
nano ~/.vnc/xstartup
```

Add the following content to `xstartup`:
```bash
#!/bin/bash
# Start XFCE environment
startxfce4 &
```

```bash
# Make the startup script executable
chmod +x ~/.vnc/xstartup

# Start VNC server
vncserver :1
```

**Network Configuration**:
- Create an inbound network rule in Azure to allow port 5901
- Connect using TigerVNC client

## Neovim Setup

### Install Neovim and Packer
```bash
# Install Packer (Neovim Plugin Manager)
git clone --depth 1 https://github.com/wbthomason/packer.nvim \
  ~/.local/share/nvim/site/pack/packer/start/packer.nvim

# Create Neovim config directory
mkdir -p ~/.config/nvim
touch ~/.config/nvim/init.lua
```

### Sample `init.lua` Configuration
```lua
-- init.lua for Neovim
-- Enable relative line numbers
vim.wo.relativenumber = true
vim.wo.number = true

-- Install and configure Packer plugin manager
return require('packer').startup(function()
  -- Package manager itself
  use 'wbthomason/packer.nvim'
  
  -- Language support plugins
  use 'neoclide/coc.nvim'  -- Autocompletion and language server support
  use 'leafgarland/typescript-vim'  -- TypeScript support
  use 'rust-lang/rust.vim'  -- Rust support
end)
```

## GitHub SSH Setup

### Generate SSH Key
```bash
# Generate SSH key
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# Start SSH agent
eval "$(ssh-agent -s)"

# Add private key to agent
ssh-add ~/.ssh/id_rsa

# Display public key to copy
cat ~/.ssh/id_rsa.pub
```

### Configure GitHub
1. Add the public key to GitHub SSH keys
2. Test connection:
   ```bash
   ssh -T git@github.com
   ```

3. Set Git global configurations
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "your_email@example.com"
   ```

## Troubleshooting
- Ensure firewall allows necessary ports
- Check network security groups in Azure
- Verify SSH key permissions (`chmod 400 your_key`)

## Notes
- Always keep your system and packages updated
- Use strong, unique passwords
- Regularly backup your configuration files
