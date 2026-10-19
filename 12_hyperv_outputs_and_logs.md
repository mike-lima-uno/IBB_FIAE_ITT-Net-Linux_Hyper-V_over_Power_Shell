# Hyper-V over Power Shell - Outputs and logs

<!-- vscode-markdown-toc -->
* 1. [Redirection and Output](#RedirectionandOutput)
	* 1.1. [Special case: Export objects to CSV](#Specialcase:ExportobjectstoCSV)
	* 1.2. [Special case XML: Export PowerShell objects in a format that preserves their structure](#SpecialcaseXML:ExportPowerShellobjectsinaformatthatpreservestheirstructure)
* 2. [Useful Output Commands](#UsefulOutputCommands)
* 3. [Error redirection](#Errorredirection)
* 4. [Error Handling](#ErrorHandling)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

##  1. <a name='RedirectionandOutput'></a>Redirection and Output

PowerShell uses redirection operators to send output to files.

```powershell
# Redirect standard output and overwrite a file 
# (the file is generated on the same folder you run it. I moved it to the folder outputs/)
Get-VM > .\outputs\vm_list.txt

# Append output to an existing file:
Get-VM >> vms.txt

# The `Out-File` command provides more control:
Get-VM | Out-File -FilePath .\outputs\vm_outfile.txt
```

###  1.1. <a name='Specialcase:ExportobjectstoCSV'></a>Special case: Export objects to CSV

```powershell
Get-VM | 
    Select-Object Name, State, Status |
    Export-Csv -Path .\outputs\vm_export.csv -NoTypeInformation
```

###  1.2. <a name='SpecialcaseXML:ExportPowerShellobjectsinaformatthatpreservestheirstructure'></a>Special case XML: Export PowerShell objects in a format that preserves their structure

```powershell
# returns a big long xml file
Get-VM |
    Export-Clixml -Path .\outputs\vm_export.xml
```

Read them again:

```powershell
$VMData = Import-Clixml .\outputs\vm_export.xml
```

##  2. <a name='UsefulOutputCommands'></a>Useful Output Commands - The famous print

`Write-Host` displays text directly:

```powershell
Write-Host "The VM was created."
```

You can use colors:

```powershell
Write-Host "Success" -ForegroundColor Green
Write-Host "Warning" -ForegroundColor Yellow
Write-Host "Error" -ForegroundColor Red
```

`Write-Output` sends data through the pipeline:

```powershell
Write-Output "This object can continue through the pipeline."
```

`Write-Warning` displays a warning:

```powershell
Write-Warning "The virtual disk already exists."
```

`Write-Error` reports an error:

```powershell
Write-Error "The VM could not be found."
```

##  3. <a name='Errorredirection'></a>Error redirection

Send errors to a file. If you work with Linux, note the similarity with the error stream.

```powershell
Get-Item "C:\DoesNotExist.txt" 2> errors.txt
```

Send both normal output and errors to the same file:

```powershell
Get-Item "C:\DoesNotExist.txt" *> output.txt
```

The variable `$null` represents no value.

##  4. <a name='ErrorHandling'></a>Error Handling

Use `try`, `catch`, and `finally` to handle errors:

```powershell
try {
    Get-VM -Name "client01" -ErrorAction Stop
    Write-Host "VM found."
}
catch {
    Write-Host "An error occurred: $($_.Exception.Message)" `
        -ForegroundColor Red
}
finally {
    Write-Host "Operation finished."
}
# An error occurred: Von Hyper-V wurde kein virtueller Computer mit dem Namen "client01" gefunden.
```

The `-ErrorAction Stop` parameter converts a non-terminating error into an error that can be caught by `catch`.

A common pattern is:

```powershell
try {
    Start-VM -Name "client01" -ErrorAction Stop
}
catch {
    Write-Error "Could not start the VM: $($_.Exception.Message)"
}
# Could not start the VM: Von Hyper-V wurde kein virtueller Computer mit dem Namen "client01" gefunden.
```
