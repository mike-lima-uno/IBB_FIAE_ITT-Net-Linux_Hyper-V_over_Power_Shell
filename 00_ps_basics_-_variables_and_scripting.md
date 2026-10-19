# PowerShell Basics - Variables and Scripting

<!-- vscode-markdown-toc -->
* 1. [Introduction to PowerShell Scripting - What is it?](#IntroductiontoPowerShellScripting-Whatisit)
* 2. [Variables in PowerShell](#VariablesinPowerShell)
	* 2.1. [Variable types](#Variabletypes)
	* 2.2. [Arrays](#Arrays)
* 3. [Creating and Running Scripts](#CreatingandRunningScripts)
	* 3.1. [Parameters](#Parameters)
* 4. [Comments](#Comments)
* 5. [Execution Policy](#ExecutionPolicy)
	* 5.1. [Practicing in a Windows 11 VM](#PracticinginaWindows11VM)
	* 5.2. [Running Commands in the Console](#RunningCommandsintheConsole)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

This is the first article in a series about configuring **Hyper-V virtual machines** with PowerShell.  
To follow and understand the articles in this series, you should be familiar with basic PowerShell commands.  

This series was created as free learning material for IBB/Viona students in the ITT-Net-Lin module.  
It was motivated by the need to configure and start virtual machines (VMs) on Hyper-V for a virtual network (September/October 2026).

This article introduces the basics of the PowerShell prompt and scripting.

What this file is not intended for:

* It is not a complete PowerShell course.
* It is not a zero-to-hero learning path.
* It does not cover every command.

##  1. <a name='IntroductiontoPowerShellScripting-Whatisit'></a>Introduction to PowerShell Scripting - What is it?

PowerShell is a command-line shell and scripting language designed for:

* system administration, 
* automation, and 
* task management. 

Unlike traditional command prompts, PowerShell works primarily with **objects**, not just text.  
This makes it possible to **retrieve**, **filter**, **modify**, and **organize** system information efficiently.

##  2. <a name='VariablesinPowerShell'></a>Variables in PowerShell

PowerShell variable names begin with the dollar sign (`$`). PowerShell is dynamically typed; it determines a variable's type from the value assigned to it.
You can display a variable by entering its name or use it in an expression:

```powershell
# assign value to a variable
$name = "client01"
$memory = 4096
$isEnabled = $true

# display
$name
# client01

# use variables in an expression
Write-Host "The virtual machine is $name"
# The virtual machine is client01

Write-Host "The VM $($name) has $($memory) MB of RAM."
# The VM client01 has 4096 MB of RAM.
```

###  2.1. <a name='Variabletypes'></a>Variable types

PowerShell is dynamically typed, but you can specify a type explicitly:

```powershell
[string]$VMName = "client01"
[int]$RAM = 4096
[bool]$Enabled = $true
```

Common types include:

```powershell
[string]  # Text
[int]     # Integer
[double]  # Decimal number
[bool]    # True or false
[array]   # Collection of values
```

###  2.2. <a name='Arrays'></a>Arrays

An array stores multiple values, and its individual elements can be accessed using a zero-based index.
The `@()` syntax is one way to create an array. You can process each item in a loop:

```powershell
$VMs = @(
    "client01",
    "client02",
    "Ubuntu-Server"
)

$VMs[0]
# client01

foreach ($VMName in $VMs) {
    Write-Host "Processing $VMName"
}
# Processing client01
# Processing client02
# Processing Ubuntu-Server
```

##  3. <a name='CreatingandRunningScripts'></a>Creating and Running Scripts

A script is a text file. PowerShell scripts use the `.ps1` extension (pronounced “P-S-one”).
A simple `create_vm.ps1` script might look like this:

Run the script from its directory using a relative path that begins with `.\`.
Here, `.\` means “from the current directory”; it is not the same as dot-sourcing a script. You can also use the script's absolute path.

```powershell
# your script create_vm.ps1
$VMName = "client01"
Write-Host "Creating VM: $VMName"
```

Save the file as `create_vm.ps1`, then run it from PowerShell:

```powershell
.\create_vm.ps1
```

###  3.1. <a name='Parameters'></a>Parameters

Scripts become more reusable when they accept parameters.

```powershell
param(
    [string]$VMName,
    [int]$MemoryMB = 4096
)

Write-Host "VM name: $VMName"
Write-Host "Memory: $MemoryMB MB"
```

Run the script like this:

```powershell
.\create-vm.ps1 -VMName "client01" -MemoryMB 4096
```

The default value is used if memory is not specified:

```powershell
.\create-vm.ps1 -VMName "client01"
```

##  4. <a name='Comments'></a>Comments

A single-line comment begins with `#`. For a block comment, use `<#` and `#>`:

```powershell
# This is a single-line comment

<#
This is a block comment.
It can span multiple lines.
#>

Write-Output "Comments do not affect this output."
```

##  5. <a name='ExecutionPolicy'></a>Execution Policy

If PowerShell prevents a script from running, check the effective execution policy:

```powershell
Get-ExecutionPolicy
```

To see the policy at each scope, run:

```powershell
Get-ExecutionPolicy -List
```

**Hint:** As a Viona student, contact Viona support through Teams if you need help running scripts on your IBB-managed laptop. Ask them to explain the applicable execution policy and the approved way to proceed. Group Policy settings managed by your organization may override policies set at other scopes.

###  5.1. <a name='PracticinginaWindows11VM'></a>Practicing in a Windows 11 VM

An alternative for practicing is to use a Windows 11 virtual machine, if your course setup and permissions allow it. Open PowerShell in that VM and run your scripts there. The VM has its own execution-policy configuration; virtualization does not automatically disable that policy or make script execution risk-free.

###  5.2. <a name='RunningCommandsintheConsole'></a>Running Commands in the Console

For short examples, you can enter or paste the commands directly into the PowerShell console instead of saving them in a `.ps1` file. Review commands before running them. This is not a way to run a script file or change the execution policy; it is only suitable for commands that are safe and practical to execute interactively.
