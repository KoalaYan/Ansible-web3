# Ansible for web3

## For Windows 
In Root Powershell
```
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
Get-WindowsCapability -Online | ? Name -like 'OpenSSH*'
Start-Service sshd
Set-Service -Name sshd -StartupType 'Automatic'
if (!(Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue | Select-Object Name, Enabled)) {
    Write-Output "Firewall Rule 'OpenSSH-Server-In-TCP' does not exist, creating it..."
    New-NetFirewallRule -Name 'OpenSSH-Server-In-TCP' -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
} else {
    Write-Output "Firewall rule 'OpenSSH-Server-In-TCP' has been created and exists."
} 
New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
```

ssh rsa login:
https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh-server-configuration

win remote ansible support:
https://support.huawei.com/enterprise/zh/doc/EDOC1100327176/734f2c0
https://cloud.tencent.com/developer/article/2115363

On host:
`pip install pywinrm`

On client:

If `Get-NetConnectionProfile` results are all `NetworkCategory=Public`
```
RemoteSigned -c "Set-NetConnectionProfile -NetworkCategory Private"
```

```
# Change powershell policy to remotesigned
set-executionpolicy remotesigned
# Open WinRM
winrm get winrm/config/service
winrm quickconfig
winrm set winrm/config/service/auth @{Basic="true"}
winrm set winrm/config/service @{AllowUnencrypted="true"}
```

Host test ping:
```
ansible windows -m win_ping -i inventory.ini
```
## For UNIX / MacOS

```
https://github.com/ipfs/kubo/releases/download/v0.34.1/kubo_v0.34.1_linux-amd64.tar.gz
tar -xvzf kubo_v0.34.1_linux-amd64.tar.gz
cd go-ipfs
sudo bash install.sh
ipfs --version
```


## Run
```
ansible-playbook -i inventory.ini playbook.yaml
```