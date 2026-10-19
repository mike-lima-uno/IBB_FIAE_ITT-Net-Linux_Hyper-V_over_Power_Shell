# Hyper-V over Power Shell - Useful Commands

<!-- vscode-markdown-toc -->
* 1. [The Pipeline](#ThePipeline)
	* 1.1. [More about Filtering with `Where-Object`](#MoreaboutFilteringwithWhere-Object)
	* 1.2. [Selecting properties](#Selectingproperties)
	* 1.3. [Sorting the result of a query](#Sortingtheresultofaquery)
	* 1.4. [Measuring and counting](#Measuringandcounting)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

##  1. <a name='ThePipeline'></a>The Pipeline

The pipeline is one of PowerShell’s most important features. It sends the output of one command to another command using the pipe character `|`.

```powershell
Get-VM | Where-Object { $_.State -eq "Running" }

# it returns for me:
# 
# Name                    State   CPUUsage(%) MemoryAssigned(M) Uptime             Status           Version
# ----                    -----   ----------- ----------------- ------             ------           -------
# ibbfirewall_opnsense    Running 0           4096              1.11:18:55.3790000 Normaler Betrieb 12.0   
# ibbg_client01           Running 0           4096              1.11:18:55.3420000 Normaler Betrieb 12.0  
# ... 
```

This means:

1. Get all VMs.
2. Keep only VMs whose state is `Running`.

The special variable `$_` represents the current object in the pipeline.

###  1.1. <a name='MoreaboutFilteringwithWhere-Object'></a>More about Filtering with `Where-Object`

```powershell
Get-ChildItem -Filter *.vhdx |
    Where-Object { $_.Length -gt 1GB }
```

This lists VHDX files larger than 1 GB.

You can filter using a property:

```powershell
Get-VM |
    Where-Object { $_.Name -like "client*" }
```

###  1.2. <a name='Selectingproperties'></a>Selecting properties

Use `Select-Object` to display only specific properties:

```powershell
Get-VM |
    Select-Object Name, State, MemoryAssigned, ProcessorCount
```

You can rename or calculate properties:

```powershell
Get-ChildItem -Filter *.vhdx |
    Select-Object FullName,
        @{Name="SizeGB"; Expression={[math]::Round($_.Length / 1GB, 2)}}
```

###  1.3. <a name='Sortingtheresultofaquery'></a>Sorting the result of a query

```powershell
Get-VM |
    Sort-Object Name
```

Sort by memory in descending order:

```powershell
Get-VM |
    Sort-Object MemoryAssigned -Descending
```

###  1.4. <a name='Measuringandcounting'></a>Measuring and counting

Count all VMs:

```powershell
(Get-VM).Count
```

Measure disk files:

```powershell
Get-ChildItem -Filter *.vhdx |
    Measure-Object -Property Length -Sum
```
