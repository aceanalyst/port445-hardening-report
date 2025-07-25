# Port 445 Hardening Report

## Summary

This report documents the security assessment and hardening process of **TCP Port 445 (Microsoft-DS)** on a Windows 10 host (`172.20.10.12`). Port 445 is commonly used for SMB file sharing and is a well-known target for attacks such as **EternalBlue**.

---

## Host Information

- **IP Address:** `172.20.10.12`
- **OS Version:** Windows 10 (Build 19045.5965)
- **Interface:** Wi-Fi

---

## Initial Scan

**Command used:**
```bash
nmap -Pn -p 445 172.20.10.12

```
**Result**

```
PORT    STATE SERVICE

445/tcp open  microsoft-ds

```
## Hardening Process

1. Opened PowerShell as Administrator

2. Checked status of SMB and Server service:

```
Get-Service | Where-Object { $_.Name -like "*smb*" -or $_.DisplayName -like "*server*" }
```

3. Disabled SMBv1 (if enabled):

```
Set-SmbServerConfiguration -EnableSMB1Protocol $false -Force
```

4. Disabled Server service:

```
Stop-Service -Name 'LanmanServer' -Force
Set-Service -Name 'LanmanServer' -StartupType Disabled
```

## Verification
After hardening, we re-scanned the same port:

Command used:
```
nmap -Pn -p 445 172.20.10.12
```

Result:
```
PORT    STATE  SERVICE
445/tcp closed microsoft-ds
```

## 📊 Summary

| Item                   | Status         |
|------------------------|----------------|
| Initial Scan           | Port 445 open  |
| Hardening Applied      | ✅ Yes         |
| Post-Hardening Scan    | Port 445 closed |

Notes

1. Port 445 (SMB) is often exploited by malware like WannaCry and EternalBlue.

2. Disabling it removes unnecessary attack surfaces.

3. For networks requiring SMB, use firewall rules instead of disabling the service entirely.

