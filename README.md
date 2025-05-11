# WSL2 Comprehensive Guide

This document provides a step‑by‑step overview of installing, configuring, and using Windows Subsystem for Linux 2 (WSL2), including advanced customizations such as mounting Google Drive.

## 1. Prerequisites

- **Windows Version**: Must be running Windows 10 (1903 or higher) or Windows 11.
- **WSL2 Feature**: Ensure Virtual Machine Platform and Windows Subsystem for Linux optional components are enabled.

## 2. Installing WSL2

1. Open **PowerShell** as Administrator.
2. Enable required features:
   ```powershell
   dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
   dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
   ```
3. Restart your PC.
4. Set WSL2 as default:
   ```powershell
   wsl --set-default-version 2
   ```
5. Install a Linux distro (e.g., Ubuntu):
   ```powershell
   wsl --install -d Ubuntu
   ```

## 3. Initial Linux Setup

1. Launch the distro from Start menu.
2. Create your Linux user account and password.
3. Update packages:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

## 4. Basic Usage and File System Access

- Windows drives auto‑mount under `/mnt`, e.g., `C:` → `/mnt/c`.
- Access Linux files from Windows via `\\wsl$\<DistroName>\home\<user>` in File Explorer.
- Run Linux commands from PowerShell:
  ```powershell
  wsl ls -la /mnt/c/Users
  ```

## 5. Automount Configuration

To customize drive mounts, edit `/etc/wsl.conf` in your distro:
```ini
[automount]
enabled = true
root = /mnt/
options = "metadata,umask=22,fmask=11"
drives = C, D, E, G
```
After saving, run:
```powershell
wsl --shutdown
```
and relaunch your distro.

## 6. Google Drive Setup

### A) Manual Mount for File Stream (G:)
1. Create mount point:
   ```bash
   sudo mkdir -p /mnt/g
   ```
2. Mount drive:
   ```bash
   sudo mount -t drvfs G: /mnt/g -o metadata,uid=$(id -u),gid=$(id -g)
   ```
3. Navigate:
   ```bash
   cd "/mnt/g/My Drive/business/YouTube"
   ```

### B) Automount Google Drive
Add `G` to drives list in `/etc/wsl.conf` (see section 5). Restart WSL.

## 7. VS Code Integration

- Install **Remote – WSL** extension in VS Code.
- Open a WSL folder with:
  ```bash
  code .
  ```
- Benefit from full Linux tooling with Windows UI.

## 8. Docker in WSL2

- Install **Docker Desktop** with WSL2 integration.
- Enable your distro in Docker settings.
- Use `docker` commands directly inside WSL.

## 9. Networking and Port Forwarding

- Windows and Linux share localhost; e.g., a server on `localhost:3000` in WSL is reachable from Windows.
- To expose Linux-only services, bind to `0.0.0.0` or configure Windows Firewall.

## 10. Exporting and Importing Distros

- **Export**:
  ```powershell
  wsl --export Ubuntu ubuntu_backup.tar
  ```
- **Import**:
  ```powershell
  wsl --import MyUbuntu D:\WSL\MyUbuntu ubuntu_backup.tar --version 2
  ```

## 11. Troubleshooting Common Errors

- **CreateProcessParseCommon mount errors**: Ensure drive letter is valid and in `/mnt`.
- **Permission issues**: Use proper `metadata` mount options in `wsl.conf`.
- **Outdated WSL2 kernel**: Update with:
  ```powershell
  wsl --update
  ```

## 12. Tips and Resources

- **Set default distro**: `wsl --set-default <DistroName>`.
- **List running distros**: `wsl -l -v`.
- **Uninstall a distro**: `wsl --unregister <DistroName>`.
- **Official docs**: https://docs.microsoft.com/windows/wsl
