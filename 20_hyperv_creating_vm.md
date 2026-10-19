# Hyper-V over Power Shell - Start and Stop

<!-- vscode-markdown-toc -->
* 1. [Commands](#Commands)
	* 1.1. [Creating ONE virtual machine](#CreatingONEvirtualmachine)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

this file is tought to introduce some Power Sheel commands  
Hier you can do the same things, we normally configure over Hyper-V GUI, but through terminal.  

This file is not intended to cover all details about scripting in Power Shell
It's assumed that the reader has some familiarity with basic comands

##  1. <a name='Commands'></a>Commands

template for script (use copy + paste)
```powershell
```

*** Always STOP THE VIRTUAL MACHINE before any changes  

*** Some pratices that make the things easier, not necessarily better or safer  
1. Name the virtual disk with the same name as the virtual machine e.g. client01 and client01.vhdx 
2. Use a variable. see file "hyperv_01..." for more details.  

###  1.1. <a name='CreatingONEvirtualmachine'></a>Creating ONE virtual machine

*** HINT: I'll assume you are conformtable using variables within Power Shell 

Basic configuration (fell free to change it. it's recommended to use variables):
* the machines created hier uses the "default switch".  
* RAM Memory: 4GB
* HD size: 20GB
* Nr of processors: 2
* safety start: deactivated
* restore points: deactivated

define the initial variables for your virtual machine.
```powershell
# Configurações
$VMName = "Ubuntu-Server"
$ISOPath = "C:\xxxx\ubuntu-26.04-live-server-amd64.iso"
$VMPath = "C:\Hyper-V\$VMName"
$VHDXPath = "$VMPath\$VMName.vhdx"
$SwitchName = "Default Switch"

# Criar a pasta da VM
New-Item -ItemType Directory -Path $VMPath -Force | Out-Null

# Criar a VM com 4 GB de RAM e disco de 20 GB
New-VM `
    -Name $VMName `
    -Generation 2 `
    -MemoryStartupBytes 4096MB `
    -NewVHDPath $VHDXPath `
    -NewVHDSizeBytes 20480MB `
    -Path $VMPath `
    -SwitchName $SwitchName

# Configurar 2 processadores virtuais
Set-VMProcessor `
    -VMName $VMName `
    -Count 2

# Desativar Secure Boot
Set-VMFirmware `
    -VMName $VMName `
    -EnableSecureBoot Off

# Desativar checkpoints/restore points
Set-VM `
    -Name $VMName `
    -CheckpointType Disabled

# Configurar memória dinâmica
Set-VMMemory `
    -VMName $VMName `
    -DynamicMemoryEnabled $false

# Conectar a ISO ao drive de DVD
Set-VMDvdDrive `
    -VMName $VMName `
    -Path $ISOPath

# Fazer a VM iniciar pelo DVD/ISO
$DVD = Get-VMDvdDrive -VMName $VMName

Set-VMFirmware `
    -VMName $VMName `
    -FirstBootDevice $DVD

# Exibir a configuração
Get-VM -Name $VMName |
    Select-Object Name, State, Generation, MemoryStartup, ProcessorCount, CheckpointType
```

Then start and show the virtual machine
```powershell
Start-VM -Name $VMName
Start-Process "vmconnect.exe" -ArgumentList "localhost", $VMName
```


Create a dynamic 20 GB virtual disk:

```powershell
New-VHD `
    -Path "C:\Hyper-V\client01.vhdx" `
    -SizeBytes 20480MB `
    -Dynamic
```