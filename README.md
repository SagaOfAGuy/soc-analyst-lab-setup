# SOC Analyst Lab Setup
SOC Analyst Lab setup documentation

## Prerequisites
- Create Windows 10 Virtual Machine
- Create Kali Linux Virtual Machine with `sliver` installed

## Terraform Windows Virtual Machine Automation
Coming soon! 

## Windows 10 Virtual Machine Configuration
Disable Windows 10 Defender in PowerShell:
https://blog.ecapuano.com/p/so-you-want-to-be-a-soc-analyst-part

We disable Windows Defender so we can detect attacks with LimaCharlie. Here are the general steps: 
1. Disable tamper protection
2. Disable Windows Defender with Group Policy Editor via `gpedit.msc`
3. Disable Windows Defender via the Registry
```bash
REG ADD "hklm\software\policies\microsoft\windows defender" /v DisableAntiSpyware /t REG_DWORD /d 1 /f
```
4. Disable Windows Defender via Safe mode
5. Prevent Windows VM going into standby mode:
```bash
powercfg /change standby-timeout-ac 0
powercfg /change standby-timeout-dc 0
powercfg /change monitor-timeout-ac 0
powercfg /change monitor-timeout-dc 0
powercfg /change hibernate-timeout-ac 0
powercfg /change hibernate-timeout-dc 0
```
6. Install `sysmon` on the Windows VM. `sysmon` is basically a program that monitors processes on Windows OS.
```bash
# Install sysmon from Microsoft site
Invoke-WebRequest -Uri https://download.sysinternals.com/files/Sysmon.zip -OutFile C:\Windows\Temp\Sysmon.zip

# Unzip zip file
expand-archive C:\Windows\Temp\Sysmon.zip
```

7. Install sysmon configuration file to properly run sysmon with high quality tracing: 
```bash
Invoke-WebRequest -Uri https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml -OutFile C:\Windows\Temp\Sysmon\sysmonconfig.xml
```

8. Run sysmon with the configuration file:
```bash
C:\Windows\Temp\Sysmon\Sysmon64.exe -accepteula -i
C:\Windows\Temp\Sysmon\sysmonconfig.xml
```

9. Confirm that `sysmon` is running:
```bash
Get-Service sysmon64
```

10. Confirm if sysmon is logging:
```bash
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" -MaxEvents 10
```

## Installing LimaCharlie
Signing up for LimaCharlie Account: https://app.limacharlie.io/signup

LimaCharlie Installation: https://blog.ecapuano.com/p/so-you-want-to-be-a-soc-analyst-part


## Kali Virtual Machine Configuration
1. Install `sliver` C2 Framework so we can fire attacks on our Windows VM
```bash
# Download Sliver Linux server binary
sudo wget https://github.com/BishopFox/sliver/releases/download/v1.5.34/sliver-server_linux -O /usr/local/bin/sliver-server

# Make it executable
sudo chmod +x /usr/local/bin/sliver-server

# install mingw-w64 for additional capabilities
sudo apt install -y mingw-w64
```

2. Create `/opt/sliver/` directory:
```bash
sudo mkdir /opt/sliver
```



