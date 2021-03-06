# asdk arm template library


Table of contents:

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Usage](#usage)

## Overview
This repository is to maintain ARM templates of FortiGate deployments on Azure Stack.

## Prerequisites
- PowerShell 5 or later
- Az module

## Usage
1. Open PowerShell and connect to Azure Stack

```powershell
$Endpoint = "https://management.local.azurestack.external"
$EnvName = "AzureStackAdmin"
Add-AzEnvironment -Name $EnvName -ARMEndpoint $Endpoint
Connect-AzAccount -Environment $EnvName
```

2. Specify ARM tempalte

```powershell
$templateFile = "path/to/template"
```

3. Available Templates

(1) Deploy FGT-HA from marketplace (worked)

```powershell
$templateFile = "./FGT-APHA_marketplace.json"
```

#Original
`https://github.com/fortinet/azure-templates/blob/main/FortiGate/FortiGate-AP-HA/mainTemplate.json`

```powershell
New-AzResourceGroupDeployment `
  -Name fgthadeploy `
  -ResourceGroupName MyResourceGroup `
  -TemplateFile $templateFile `
  -location "local" `
  -FortiGateNamePrefix "FGHA" `
  -publicIPNewOrExisting "new" `
  -publicIP2NewOrExisting "new" `
  -publicIP3NewOrExisting "new" `
  -publicIPAddressName "FGTAPClusterPublicIP" `
  -publicIPAddressResourceGroup "MyResourceGroup" `
  -publicIPAddress2Name "FGTAMgmtPublicIP" `
  -publicIPAddress2ResourceGroup "MyResourceGroup" `
  -publicIPAddress3Name "FGTBMgmtPublicIP" `
  -publicIPAddress3ResourceGroup "MyResourceGroup" `
  -publicIPAddressType "Static" `
  -vnetNewOrExisting "new" `
  -vnetName "FGHAVnet" `
  -vnetResourceGroup "MyResourceGroup" `
  -vnetAddressPrefix "10.2.0.0/16" `
  -Subnet1Name "EntrySubnet" `
  -Subnet1Prefix "10.2.1.0/24" `
  -Subnet2Name "TransitSubnet" `
  -Subnet2Prefix "10.2.2.0/24" `
  -FGT-A-IP-Subnet2 "10.2.2.4" `
  -Subnet3Name "HASyncSubnet" `
  -Subnet3Prefix "10.2.3.0/28" `
  -Subnet4Name "ManagementSubnet" `
  -Subnet4Prefix "10.2.4.0/28" `
  -Subnet5Name "ProtectedSubnet" `
  -Subnet5Prefix "10.2.5.0/24" `
  -Debug `
  -Verbose
```

(2) Deploy FGT-HA from blob image

```powershell
$templateFile = "./FGT-APHA_blob.json"
```

#combined the following
`https://github.com/fortinet/azure-templates/blob/main/FortiGate/FortiGate-AP-HA/mainTemplate.json`

`https://github.com/fortinetsolutions/Azure-Templates/blob/master/FortiGate/Others/%20non-Marketplace%20Single%20VM%20Deployment/mainTemplate.json`

1. Create ResourceGroup, StorageAccount and Container ("images" and "vhds").
2. Convert VHD image to fixed.
   Convert-VHD -Path .\fortios.vhd -DestinationPath .\fortios642.vhd -VHDType Fixed
3. Upload the VHD on container named images.
4. Run the following deployment.


```powershell
New-AzResourceGroupDeployment `
  -Name fgthadeploy `
  -ResourceGroupName user01resourcegroup01 `
  -TemplateFile $templateFile `
  -location "local" `
  -FortiGateNamePrefix "FGHA" `
  -StorageAccountName "user01storage" `
  -publicIPNewOrExisting "new" `
  -publicIP2NewOrExisting "new" `
  -publicIP3NewOrExisting "new" `
  -publicIPAddressName "FGTAPClusterPublicIP" `
  -publicIPAddressResourceGroup "user01resourcegroup01" `
  -publicIPAddress2Name "FGTAMgmtPublicIP" `
  -publicIPAddress2ResourceGroup "user01resourcegroup01" `
  -publicIPAddress3Name "FGTBMgmtPublicIP" `
  -publicIPAddress3ResourceGroup "user01resourcegroup01" `
  -publicIPAddressType "Static" `
  -vnetNewOrExisting "new" `
  -vnetName "FGHAVnet" `
  -vnetResourceGroup "user01resourcegroup01" `
  -vnetAddressPrefix "10.0.0.0/16" `
  -Subnet1Name "EntrySubnet" `
  -Subnet1Prefix "10.0.1.0/24" `
  -Subnet2Name "TransitSubnet" `
  -Subnet2Prefix "10.0.2.0/24" `
  -Subnet3Name "HASyncSubnet" `
  -Subnet3Prefix "10.0.3.0/28" `
  -Subnet4Name "ManagementSubnet" `
  -Subnet4Prefix "10.0.4.0/28" `
  -Subnet5Name "ProtectedSubnet" `
  -Subnet5Prefix "10.0.5.0/24" `
  -imagename "fortios623.vhd"
```

(3) Deploy FMG from blob image

```powershell
$templateFile = "./fmgtemplate.json"
```

1. Create ResourceGroup, StorageAccount and Container ("images" and "vhds").
2. Convert VHD image to fixed.
   Convert-VHD -Path .\fmg.vhd -DestinationPath .\fmg642.vhd -VHDType Fixed
3. Upload the VHD on container named images.
4. Run the following deployment.

```powershell
$resourcegroup = "MyResourceGroup"
$storageaccount = "MyStorage"
$imagename = "fmg642.vhd"

New-AzResourceGroupDeployment `
  -Name fmgdeploy `
  -ResourceGroupName $resourcegroup `
  -TemplateFile $templateFile `
  -location "local" `
  -FortiManagerName "FortiManager" `
  -StorageAccountName $storageaccount `
  -instanceType "Standard_D2" `
  -publicIPNewOrExistingOrNone "new" `
  -publicIPAddressName "FortiManager-PublicIP" `
  -publicIPResourceGroup $resourcegroup `
  -publicIPAddressType "Static" `
  -vnetNewOrExisting "new" `
  -vnetName "FTNTVnet" `
  -vnetResourceGroup $resourcegroup `
  -vnetAddressPrefixes "10.1.0.0/16" `
  -subnetName "FortiManagerSubnet" `
  -subnetPrefix "10.1.10.0/24" `
  -FortiManagerPrivateIP "10.1.10.4" `
  -imagename $imagename `
  -Debug `
  -Verbose
```
