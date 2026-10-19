# Hyper-V over Power Shell - Basic Commands

<!-- vscode-markdown-toc -->
* 1. [Listing and getting Vms](#ListingandgettingVms)
* 2. [Listing and getting virtual disks connected to VMs:](#ListingandgettingvirtualdisksconnectedtoVMs:)
* 3. [Starting and stopping VMs](#StartingandstoppingVMs)
* 4. [Connecting to a VM](#ConnectingtoaVM)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

Here we start specificaly with commands that interact directly with virtual machines doing the same you can do manualy on Hyper-V.

##  1. <a name='ListingandgettingVms'></a>Listing and getting Vms

PowerShell includes commands for managing Hyper-V.

List all virtual machines:

```powershell
Get-VM

# returns for me
# 
# Name                            State   CPUUsage(%) MemoryAssigned(M) Uptime             Status           Version
# ----                            -----   ----------- ----------------- ------             ------           -------
# alpine_terminal_3_24_1          Off     0           0                 00:00:00           Normaler Betrieb 12.0 
# freebsd_xorg_15_1               Off     0           0                 00:00:00           Normaler Betrieb 12.0   
# ibbfirewall_opnsense            Running 0           4096              1.09:58:36.9060000 Normaler Betrieb 12.0   
# ibbg_client01                   Running 0           4096              1.09:58:36.7030000 Normaler Betrieb 12.0   
```

Show a specific VM:

```powershell
Get-VM -Name "client01"
# returns one line as above. for me, the right command is > Get-VM -Name "ibbg_client01"
```

Show the VM state. Note the Pipe.

```powershell
Get-VM -Name "client01" |
    Select-Object Name, State, Status
```

##  2. <a name='ListingandgettingvirtualdisksconnectedtoVMs:'></a>Listing and getting virtual disks connected to VMs:

```powershell
Get-VM | Get-VMHardDiskDrive

# it returns for me
# 
# VMName                          ControllerType ControllerNumber ControllerLocation DiskNumber Path
# ------                          -------------- ---------------- ------------------ ---------- ----
# alpine_terminal_3_24_1          SCSI           0                0                             C:\ProgramData\Microsoft\Windows\...
# freebsd_xorg_15_1               SCSI           0                0                             C:\ProgramData\Microsoft\Windows\...
# ibbfirewall_opnsense            SCSI           0                0                             C:\ProgramData\Microsoft\Windows\...
# ibbg_client01                   SCSI           0                0                             C:\ProgramData\Microsoft\Windows\...
```

List all `.vhdx` files in a directory:

```powershell
Get-ChildItem `
    -Path "C:\Hyper-V" `
    -Filter *.vhdx `
    -File `
    -Recurse
```

Show information about a virtual disk:

```powershell
Get-VHD -Path "C:\Hyper-V\client01\client01.vhdx"
```

##  3. <a name='StartingandstoppingVMs'></a>Starting and stopping VMs

```powershell
# start by name
Start-VM -Name "client01"

# stop by name
Stop-VM -Name "client01"

# forces stop
Stop-VM -Name "client01" -Force
```

**HINT**: starting a VM is not connecting to a VM.
Start is the process to **run the machine on**. Connect it about to show / to open the machine on its own window.
When connecting to a stopped machine, it does nont starts, instead you is shown the start button.

**HINT**: To start a machine dont connect automaticaly to it and connect dont starts a machine automaticaly.

##  4. <a name='ConnectingtoaVM'></a>Connecting to a VM

```powershell
vmconnect.exe Localhost "client01"
```

**HINT**: frequently you'll need to start more machines at once on the <inicio da aula>. create you script with all vm names you want to start, e.g. server01, server02, server_db, nas, etc; then connect only if needed. It's faster the by click on hyper-V GUI.
