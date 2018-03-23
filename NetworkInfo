# Change the next line to connect to your vCenter Server
Connect-VIServer MYVISERVER
 
$NetworkInfo = @()
Foreach ($VMHost in (Get-View -ViewType HostSystem | Where {$_.Runtime.ConnectionState -ne "disconnected"})){
    Write $VMHost.Name
    $NetworkSystem = Get-View $VMHost.ConfigManager.NetworkSystem
    Foreach ($PG in $NetworkSystem.NetworkInfo.PortGroup){
        $Details = "" | Select VMHost, vSwitch, PortGroup, ActiveNics, StandbyNics
        $Details.VMHost = $VMHost.Name
        $Details.Portgroup = $PG.Spec.Name
        If ((($PG.ComputedPolicy.NicTeaming.NicOrder.ActiveNic | Select -ExpandProperty $ActiveNic).Length) -gt 1){
            $Details.ActiveNics = [string]::join(';',($PG.ComputedPolicy.NicTeaming.NicOrder.ActiveNic | Select -ExpandProperty $ActiveNic))
        }
        Else {
            $Details.ActiveNics = ($PG.ComputedPolicy.NicTeaming.NicOrder.ActiveNic | Select -ExpandProperty $ActiveNic)
        }
        If ((($PG.ComputedPolicy.NicTeaming.NicOrder.StandbyNic | Select -ExpandProperty $StandbyNic).Length) -gt 1){
            $Details.StandbyNics = [string]::join(';',($PG.ComputedPolicy.NicTeaming.NicOrder.StandbyNic | Select -ExpandProperty $StandbyNic))
        }
        Else{
            $Details.StandbyNics = ($PG.ComputedPolicy.NicTeaming.NicOrder.StandbyNic | Select -ExpandProperty $StandbyNic)
        }
        Foreach ($VS in $NetworkSystem.NetworkInfo.vSwitch){
            If ($VS.Name -eq $PG.Spec.vSwitchName){
                $Details.vSwitch = $VS.Name
                }
        }
        $NetworkInfo += $Details
    }
}
$NetworkInfo | Sort VMHost, VSwitch, PortGroup | Export-Csv $Filename -NoTypeInformation
Invoke-Item $filename