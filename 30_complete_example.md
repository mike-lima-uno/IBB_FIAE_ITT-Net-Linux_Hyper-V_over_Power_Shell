# <a name='ACompleteExample'></a>A Complete Example

<!-- vscode-markdown-toc -->

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

The following script creates one Hyper-V VM with 4 GB of RAM, 20 GB of storage, two virtual processors, disabled Secure Boot, disabled checkpoints, and an ISO image.

```powershell
param(
    [string]$VMName = "Ubuntu-Server",
    [string]$ISOPath = "C:\ISO\ubuntu-26.04-live-server-amd64.iso",
    [string]$SwitchName = "Default Switch"
)

$VMPath = "C:\Hyper-V\$VMName"
$VHDXPath = Join-Path $VMPath "$VMName.vhdx"

try {
    if (-not (Test-Path $ISOPath)) {
        throw "ISO file not found: $ISOPath"
    }

    if (Get-VM -Name $VMName -ErrorAction SilentlyContinue) {
        throw "A VM named '$VMName' already exists."
    }

    New-Item `
        -ItemType Directory `
        -Path $VMPath `
        -Force | Out-Null

    New-VM `
        -Name $VMName `
        -Generation 2 `
        -MemoryStartupBytes 4096MB `
        -NewVHDPath $VHDXPath `
        -NewVHDSizeBytes 20480MB `
        -Path $VMPath `
        -SwitchName $SwitchName `
        -ErrorAction Stop

    Set-VMProcessor `
        -VMName $VMName `
        -Count 2

    Set-VMFirmware `
        -VMName $VMName `
        -EnableSecureBoot Off

    Set-VM `
        -Name $VMName `
        -CheckpointType Disabled

    Set-VMDvdDrive `
        -VMName $VMName `
        -Path $ISOPath

    $DVD = Get-VMDvdDrive -VMName $VMName

    Set-VMFirmware `
        -VMName $VMName `
        -FirstBootDevice $DVD

    Write-Host "VM created successfully: $VMName" `
        -ForegroundColor Green

    Start-VM -Name $VMName

    Start-Process `
        "vmconnect.exe" `
        -ArgumentList "localhost", $VMName
}
catch {
    Write-Error "VM creation failed: $($_.Exception.Message)"
}
```

This example demonstrates variables, parameters, validation, error handling, paths, Hyper-V commands, and the use of external programs.

PowerShell becomes significantly more powerful when commands are combined through the pipeline and when scripts are written to work with objects. A good progression is to learn the basic commands first, then practice filtering, selecting, exporting, error handling, and automation with real administrative tasks.