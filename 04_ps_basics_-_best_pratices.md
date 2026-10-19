# Best Practices

<!-- vscode-markdown-toc -->

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

Use the full command names when learning PowerShell:

```powershell
Get-ChildItem
```

instead of only:

```powershell
dir
```

Aliases such as `dir`, `cd`, and `rm` are convenient interactively, but full command names make scripts easier to understand across different environments.

Use meaningful variable names:

```powershell
$VMName = "client01"
$VHDXPath = "C:\Hyper-V\client01.vhdx"
```

Avoid unclear names:

```powershell
$x = "client01"
```

Always quote paths that contain spaces:

```powershell
Get-Item "C:\Virtual Machines\client01.vhdx"
```

Check whether a resource exists before creating or deleting it:

```powershell
if (Test-Path $VHDXPath) {
    Write-Host "The disk already exists."
}
```

Use `-ErrorAction Stop` when errors must be handled:

```powershell
Get-VM -Name $VMName -ErrorAction Stop
```

Do not use `-Force` unnecessarily. It is useful for automation, but it can bypass confirmations and perform destructive actions.

Use `Set-StrictMode` in larger scripts:

```powershell
Set-StrictMode -Version Latest
```

This helps detect misspelled or undefined variables.

Use clear formatting and comments:

```powershell
# Stop the VM before modifying its virtual disk
Stop-VM -Name $VMName -Force
```

Test destructive commands before executing them. For example, first list the files:

```powershell
Get-ChildItem "C:\Hyper-V"
```

Then delete only the intended file:

```powershell
Remove-Item "C:\Hyper-V\client01.vhdx" -Force
```

Use `-WhatIf` when available to preview an operation:

```powershell
Remove-Item "C:\Hyper-V\client01.vhdx" -WhatIf
```

The command shows what would happen without actually deleting the file.
