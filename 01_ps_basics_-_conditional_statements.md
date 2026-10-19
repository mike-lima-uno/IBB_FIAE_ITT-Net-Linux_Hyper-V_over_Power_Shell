# PowerShell Basics - Variables and Scripting

<!-- vscode-markdown-toc -->
* 1. [Conditional Statements](#ConditionalStatements)
* 2. [Loops](#Loops)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

##  1. <a name='ConditionalStatements'></a>Conditional Statements

Use `if` to make decisions:

```powershell
# ensure you have the VM
$VMName = "client01"
$VM = Get-VM -Name $VMName

if ($VM.State -eq "Running") {
    Write-Host "The VM is running."
}
elseif ($VM.State -eq "Off") {
    Write-Host "The VM is off."
}
else {
    Write-Host "The VM is in state: $($VM.State)"
}
```

Common comparison operators include:

```powershell
-eq   # Equal
-ne   # Not equal
-gt   # Greater than
-ge   # Greater than or equal
-lt   # Less than
-le   # Less than or equal
-like # Pattern matching
```

Example:

```powershell
if ($RAM -ge 4096) {
    Write-Host "The VM has at least 4 GB of RAM."
}
```

##  2. <a name='Loops'></a>Loops

A `foreach` loop processes a collection:

```powershell
$VMs = @("client01", "client02", "client03")

foreach ($VMName in $VMs) {
    Start-VM -Name $VMName
}
```

A `for` loop uses a counter:

```powershell
for ($i = 1; $i -le 5; $i++) {
    Write-Host "Number: $i"
}
```

A `while` loop continues while a condition is true:

```powershell
$count = 1

while ($count -le 3) {
    Write-Host "Count: $count"
    $count++
}
```
