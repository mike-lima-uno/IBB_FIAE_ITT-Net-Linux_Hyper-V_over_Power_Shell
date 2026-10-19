# PowerShell Basics - Navigation and Basic Commands

<!-- vscode-markdown-toc -->
* 1. [ Basic Navigation and File Commands](#BasicNavigationandFileCommands)
	* 1.1. [`pwd` and `Get-Location`](#pwdandGet-Location)
	* 1.2. [`cd` and `Set-Location`](#cdandSet-Location)
	* 1.3. [`dir` and `Get-ChildItem`](#dirandGet-ChildItem)
	* 1.4. [`mkdir` and `New-Item`](#mkdirandNew-Item)
	* 1.5. [`Copy-Item`](#Copy-Item)
	* 1.6. [`Move-Item`](#Move-Item)
	* 1.7. [`Remove-Item`](#Remove-Item)
* 2. [ `Get-Item`, `Test-Path`, and File Information](#Get-ItemTest-PathandFileInformation)
	* 2.1. [`Get-Item`](#Get-Item)
	* 2.2. [`Test-Path`](#Test-Path)
	* 2.3. [`Join-Path`](#Join-Path)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

This article introduces basic navigation and commonly used PowerShell commands.

**Hint:** Some commands may fail if your account does not have the required permissions on your Viona-managed machine.  
If your course setup and permissions allow it, you can practice in a Windows 11 VM on Hyper-V.

##  1. <a name='BasicNavigationandFileCommands'></a> Basic Navigation and File Commands

PowerShell cmdlets commonly use a verb-noun format. For example, `Get-Location` is a cmdlet, and `pwd` is one of its aliases.

###  1.1. <a name='pwdandGet-Location'></a>`pwd` and `Get-Location`

Show the current location (current directory), in other words, “Where am I?”

```powershell
pwd

# pwd is an alias for:
Get-Location
```

###  1.2. <a name='cdandSet-Location'></a>`cd` and `Set-Location`

```powershell
# Change directory using an absolute path:
cd C:\Scripts

# The command above is equivalent to:
Set-Location C:\Scripts

# Move to the parent directory (one level up).
cd ..

# Move to the root of the current drive:
cd \

# Move to your home folder:
cd ~

# Change to another drive. This uses the drive letter, not cd:
D:
```

###  1.3. <a name='dirandGet-ChildItem'></a>`dir` and `Get-ChildItem`

```powershell
# List files and directories in the current directory:
dir

# List files and directories in another folder using an absolute path:
dir c:\Scripts

# Use a relative path:
dir .\my_folder\

# dir is an alias for:
Get-ChildItem
```

Use `-Filter` to list only files that match a pattern, such as `.vhdx` files:

```powershell
Get-ChildItem -Filter *.vhdx
```

Search recursively. Searching from `C:\` can take a long time and may report access errors; 
use a narrower starting path when possible.

```powershell
Get-ChildItem -Path C:\ -Filter *.vhdx -File -Recurse
```

Useful options include:

```powershell
# `-File` lists only files.
Get-ChildItem -File

# `-Directory` lists only directories.
Get-ChildItem -Directory

# `-Force` includes hidden and system items; it does not bypass access permissions.
Get-ChildItem -Force
```

###  1.4. <a name='mkdirandNew-Item'></a>`mkdir` and `New-Item`

```powershell
# Create a directory:
mkdir C:\Scripts

# is equals to:
New-Item -ItemType Directory -Path C:\Scripts
```

`New-Item` can create different kinds of items:

```powershell
# Create an empty file named example.txt in C:\Scripts:
New-Item -ItemType File -Path C:\Scripts\example.txt
```

###  1.5. <a name='Copy-Item'></a>`Copy-Item`

**Hint:** In the examples below, the backtick (`` ` ``) at the end of a line continues the command on the next line. Do not add spaces after it.

Copy a file:

```powershell
Copy-Item `
    -Path "C:\Hyper-V\Ubuntu-Base.vhdx" `
    -Destination "C:\Hyper-V\client01.vhdx"
```

Copy a directory and its contents recursively. The destination parent directory (`C:\Backup` in this example) must already exist.

```powershell
Copy-Item `
    -Path "C:\Scripts" `
    -Destination "C:\Backup\Scripts" `
    -Recurse
```

###  1.6. <a name='Move-Item'></a>`Move-Item`

Move or rename a file:

```powershell
Move-Item `
    -Path "C:\Scripts\old.ps1" `
    -Destination "C:\Scripts\new.ps1"
```

###  1.7. <a name='Remove-Item'></a>`Remove-Item`

**Caution:** `-Force` is not a safety feature. It can allow removal of read-only or hidden items, but it does not bypass access permissions.

```powershell
# Delete a file:
Remove-Item "C:\Hyper-V\old-disk.vhdx"

# Remove a read-only file:
Remove-Item "C:\Hyper-V\old-disk.vhdx" -Force

# Preview removal of a directory and its contents:
Remove-Item "C:\OldScripts" -Recurse -WhatIf
```

`-WhatIf` previews the action without performing it. After checking the target path and preview, remove `-WhatIf` only if you intend to perform the deletion. `Remove-Item` does not send deleted items to the Recycle Bin.

##  2. <a name='Get-ItemTest-PathandFileInformation'></a> `Get-Item`, `Test-Path`, and File Information

###  2.1. <a name='Get-Item'></a>`Get-Item`

`Get-Item` retrieves a specific file, directory, registry key, or other PowerShell provider item:

```powershell
Get-Item "C:\Hyper-V\client01.vhdx"
```

View selected properties:

```powershell
Get-Item "C:\Hyper-V\client01.vhdx" |
    Select-Object FullName, Length, CreationTime, LastWriteTime
```

Display the file size in gibibytes. PowerShell's `1GB` constant represents 1,073,741,824 bytes (1024³ bytes).

```powershell
$file = Get-Item "C:\Hyper-V\client01.vhdx"

$file | Select-Object `
    FullName,
    @{Name="SizeGiB"; Expression={[math]::Round($_.Length / 1GB, 2)}}
```

###  2.2. <a name='Test-Path'></a>`Test-Path`

Check whether a file or directory exists:

```powershell
Test-Path "C:\Hyper-V\client01.vhdx"

# The result is either:
# True
# False
```

This is useful before creating or deleting files when scripting:

```powershell
$Path = "C:\Hyper-V\client01.vhdx"

if (Test-Path $Path) {
    Write-Host "The file exists."
}
else {
    Write-Host "The file does not exist."
}
```

###  2.3. <a name='Join-Path'></a>`Join-Path`

Combine path segments using the appropriate path separator instead of concatenating strings manually. Quote paths that contain spaces.

```powershell
$Folder = "C:\Hyper-V"
$FileName = "client01.vhdx"

$Path = Join-Path $Folder $FileName
```
