# Phase 1 - Infrastructure

### VM Creation

- Created a virtual machine using VMware Workstation
- Selected Custom configuration for full control
- Disabled Easy Install to manually configure the OS installation

### ISO Configuration

- Attached Windows Server 2022 ISO manually
- Enabled “Connect at power on” for CD/DVD

### Issue Encountered

- VM attempted to boot using EFI network (PXE)
- This prevented the Windows installer from loading

### Fix

- Accessed boot menu and selected CD/DVD manually
- Successfully booted into the Windows Server installer

### Outcome

- Windows Server 2022 installation started successfully
- Server environment is now accessible via Server Manager

![VMwareWorkstation]https://github.com/HooderTheSpooder/CyberSecurity_Portfolio/blob/main/Enterprise-Active-Directory-Cybersecurity-Lab/Screenshots/01.png
