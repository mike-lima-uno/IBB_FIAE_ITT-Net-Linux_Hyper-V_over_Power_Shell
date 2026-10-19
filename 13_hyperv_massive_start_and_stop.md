# Hyper-V over Power Shell - Massive start / stop of virtual machines

<!-- vscode-markdown-toc -->

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

*** This section is a scripting section i.e. you could save it in a script.ps1 (read P-S-One) and run it on Power Shell.  

*** The problem hier is that, as an IBB / Viona student you don't have the necessary permissions to run the script and possibly won't receive it. 

*** As solution you can create your script file, ... BUT ... copy the list definition on PS, then the foreach-loop. It should good work! :)

First set a list of virtual machines, the loop it.  
**ATTENTION !!!** The last item in the list hasn't a comma. It raises error on PS.  

**HINT**: the script under starts but not connect.

```powershell
$vmlist = @(
    "server01",
    "client01",
    "client02"
)

foreach ($vmname in $vmlist) {
    $vm = Get-VM -Name $vmname

    if ($vm.State -ne "Running") {
        Start-VM -Name $vmname
		Start-Process "vmconnect.exe" -ArgumentList "localhost", $vmname
		
		# or the one-line-solution
        # Start-VM -Name $vmname; Start-Process "vmconnect.exe" -ArgumentList "localhost", $vmname
		
        Write-Host "$vmname started."
		# comment this line to avoid waiting time, but it's recommended to wait some seconds.
        Start-Sleep -Seconds 10
    }
}

```

You can use this recursive approach (with caution) for anything, also for create and remove machines and virtual discs.  
