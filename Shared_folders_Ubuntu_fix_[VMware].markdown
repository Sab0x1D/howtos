# VMware Shared Folders Setup on Ubuntu (Fix & Guide)

This guide documents how to enable and troubleshoot VMware Shared Folders on an Ubuntu guest VM.  
It covers both the **FUSE-based mount method** and the **`.vmx` configuration fix** if VMware fails to present the shared folder interface.

---

## 1. Problem

- Shared folder configured in VMware but **not appearing** inside Ubuntu.
- `vmhgfs` kernel module is missing (deprecated in modern Ubuntu kernels).
- Mount command `.host:/` fails due to missing interface.

---

## 2. Standard Fix (Working Solution)

### Step 1 – Install Required Tools
```bash
sudo apt update
sudo apt install -y open-vm-tools open-vm-tools-desktop
```

---

### Step 2 – Create Mount Point
```bash
sudo mkdir -p /mnt/hgfs
```

---

### Step 3 – Mount Shared Folder Using FUSE
```bash
sudo vmhgfs-fuse .host:/ /mnt/hgfs -o allow_other
```

---

### Step 4 – Verify
```bash
ls -la /mnt/hgfs
```
You should see your shared folder (e.g., `vpn-gateway-manager`) listed.

---

## 3. Auto-Mount on Boot (Optional)

Edit `/etc/fstab\` and add:
```bash
.host:/    /mnt/hgfs    fuse.vmhgfs-fuse    allow_other    0    0
```

---

## 4. If Mount Still Fails – Edit the `.vmx` File

When VMware does not expose shared folders, force-enable them by editing the VM configuration:

1. **Shut down the Ubuntu VM.**  
2. Open the VM’s `.vmx` file:  
   on macOS located under `~/Documents/Virtual Machines.localized/YourVM.vmwarevm/YourVM.vmx`.  
   on Windows located under `C:\Users\<your_username>\Virtual Machines\<YourVM_name>\YourVM_name.vmx`.  
4. Add the following lines at the end:

```ini
isolation.tools.hgfsServerSet.disable = "FALSE"
isolation.tools.hgfs.disable = "FALSE"
sharedFolder.maxNum = "3"
sharedFolder0.enabled = "TRUE"
sharedFolder0.readAccess = "TRUE"
sharedFolder0.writeAccess = "TRUE"
sharedFolder0.autoMount = "TRUE"
sharedFolder0.hostPath = "/Users/youruser/path/to/yourfoldername"
sharedFolder0.guestName = "yourfoldername"
sharedFolder0.expiration = "never"
```

> Replace `/Users/youruser/path/to/yourfoldername` with the actual folder path on your host.

4. Save the file and restart the VM.  
5. Remount using the same FUSE command:

```bash
sudo vmhgfs-fuse .host:/ /mnt/hgfs -o allow_other
```

---

## 5. Quick Remount Command

For future boots, you can quickly re-mount the shared folder with:
```bash
sudo mkdir -p /mnt/hgfs && sudo vmhgfs-fuse .host:/ /mnt/hgfs -o allow_other && ls -la /mnt/hgfs
```

---

## 6. Summary

- `vmhgfs` kernel module is no longer required; FUSE (`vmhgfs-fuse`) handles mounts.  
- If `.host:/` mount fails, manually configure the `.vmx` file to re-enable shared folder presentation.  
- After fixing `.vmx`, the shared folder mounts successfully under `/mnt/hgfs`.

---
