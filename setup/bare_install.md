# Windows ARM64 Setup from bare metal

This is on a Windows Pro 11 build 10.0.26100 machine.

Install Visual Studio community edition from
https://visualstudio.microsoft.com with options:

A recent installation of Windows should have the [App Installer
application](https://learn.microsoft.com/en-us/windows/msix/app-installer/install-update-app-installer).  This includes the `winget` command line installer.

Use `winget search something` to search for suitable installers.

## Install some useful tools

```powershell
winget install Mozilla.Firefox
winget install BurntSushi.ripgrep.GNU
winget install vim.vim
```

Put vim on PATH:

```
$prev_env = [Environment]::GetEnvironmentVariable("PATH", "User")
$vim_path = "$env:PROGRAMFILES\Vim\vim91"
[Environment]::SetEnvironmentVariable("PATH", "$prev_env;$vim_path", "User")
```

## SSH server

[Set up SSH
server](https://medium.com/@lilnya79/setting-up-ssh-server-and-opening-port-22-on-windows-ff9f324823b7):
from an Administrator Terminal:

```powershell
Add-WindowsCapability -Online -Name openssh.server
Start-Service sshd
Set-Service -Name sshd -StartupType 'Automatic'
New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
```

[Configure SSH shell as
Powershell](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh-server-configuration):

```powershell
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -PropertyType String -Force
```

Copy any authorized keys to file named below, then:

```powershell
icacls.exe "C:\ProgramData\ssh\administrators_authorized_keys" /inheritance:r /grant "Administrators:F" /grant "SYSTEM:F"
```

## Other useful setup

Enable remote desktop (search for remote desktop settings, enable).

Windows search for "lid" and select "Do Nothing" for close lid when powered
(to use a server with lid closed).

## Self-hosted runner

See
<https://github.com/matthew-brett/scipy-windows-arm/settings/actions/runners/new?arch=arm64&os=win>.
I chose the defaults througout.

The runner runs only on pushes to the `main` branch.
