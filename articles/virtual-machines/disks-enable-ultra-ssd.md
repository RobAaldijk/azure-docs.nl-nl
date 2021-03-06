---
title: Ultra schijven voor virtuele machines-Azure Managed disks
description: Meer informatie over Ultra schijven voor virtuele Azure-machines
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/28/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: d7718ebbbf4f9dec3519ce46e5d0d1cdbb5a7460
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745966"
---
# <a name="using-azure-ultra-disks"></a>Azure Ultra Disk gebruiken

In dit artikel wordt uitgelegd hoe u een ultra schijf implementeert en gebruikt, voor conceptuele informatie over Ultra disks, de [schijf typen die beschikbaar zijn in azure](disks-types.md#ultra-disk).

Azure Ultra disks biedt hoge door Voer, hoge IOPS en een consistente schijf opslag met lage latentie voor Azure IaaS virtual machines (Vm's). Deze nieuwe aanbieding biedt de hoogste prestaties van de lijn op dezelfde beschikbaarheids niveaus als onze bestaande schijven. Een belang rijk voor deel van ultra schijven is de mogelijkheid om de prestaties van de SSD in combi natie met uw workloads dynamisch te wijzigen zonder dat u uw Vm's opnieuw hoeft op te starten. Ultraschijven zijn geschikt voor gegevensintensieve workloads, zoals SAP HANA, databases in de bovenste laag en workloads met veel transacties.

## <a name="ga-scope-and-limitations"></a>GA bereik en beperkingen

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>De beschik baarheid van de VM-grootte en-regio bepalen

### <a name="vms-using-availability-zones"></a>Vm's met beschikbaarheids zones

Als u ultra disks wilt gebruiken, moet u bepalen in welke beschikbaarheids zone u zich bevindt. Niet elke regio ondersteunt elke VM-grootte met ultra schijven. Als u wilt bepalen of uw regio, zone en VM-grootte Ultra Disk ondersteunen, voert u een van de volgende opdrachten uit. Zorg ervoor dat u de **regio** -, **vmSize** -en **abonnements** waarden eerst vervangt:

#### <a name="cli"></a>CLI

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
$sku = (Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})
if($sku){$sku[0].LocationInfo[0].ZoneDetails} Else {Write-host "$vmSize is not supported with Ultra Disk in $region region"}
```

Het antwoord komt overeen met het onderstaande formulier, waarbij X de zone is die moet worden gebruikt voor de implementatie in de gekozen regio. X kan 1, 2 of 3 zijn.

De waarde **van de zone behouden** , het vertegenwoordigt uw beschikbaarheids zone en u hebt deze nodig om een ultra schijf te kunnen implementeren.

|ResourceType  |Naam  |Locatie  |Zones  |Beperking  |Mogelijkheid  |Waarde  |
|---------|---------|---------|---------|---------|---------|---------|
|cd's     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Als er geen antwoord is ontvangen van de opdracht, wordt de geselecteerde VM-grootte niet ondersteund met ultra disks in de geselecteerde regio.

Nu u weet in welke zone u wilt implementeren, volgt u de implementatie stappen in dit artikel om een virtuele machine te implementeren met een ultra schijf die is gekoppeld of een ultra schijf aan een bestaande virtuele machine toe te voegen.

### <a name="vms-with-no-redundancy-options"></a>Vm's zonder opties voor redundantie

Ultra schijven die zijn geïmplementeerd in geselecteerde regio's, moeten nu worden geïmplementeerd zonder enige redundantie opties. Niet elke schijf grootte die ondersteuning biedt voor Ultra schijven kan zich echter in deze regio bevinden. U kunt een van de volgende code fragmenten gebruiken om te bepalen welke schijf grootten ondersteuning bieden voor Ultra disks. Zorg ervoor dat u de `vmSize` waarden en het `subscription` eerst vervangt:

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

Het antwoord ziet er ongeveer uit als in het volgende formulier, `UltraSSDAvailable   True` geeft aan of de VM-grootte Ultra schijven in deze regio ondersteunt.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Een ultra schijf implementeren met behulp van Azure Resource Manager

Bepaal eerst welke VM-grootte moet worden geïmplementeerd. Zie de sectie [Ga naar bereik en beperkingen](#ga-scope-and-limitations) voor een lijst met ondersteunde VM-grootten.

Als u een virtuele machine met meerdere Ultra schijven wilt maken, raadpleegt u het voor beeld [een VM met meerdere Ultra schijven maken](https://aka.ms/ultradiskArmTemplate).

Als u uw eigen sjabloon wilt gebruiken, moet u ervoor zorgen dat **apiVersion** voor `Microsoft.Compute/virtualMachines` en `Microsoft.Compute/Disks` is ingesteld als `2018-06-01` (of hoger).

Stel de schijf-SKU in op **UltraSSD_LRS** en stel vervolgens de schijf capaciteit, IOPS, beschikbaarheids zone en door Voer in Mbps in om een ultra schijf te maken.

Zodra de VM is ingericht, kunt u de gegevens schijven partitioneren en Format teren, en configureren voor uw workloads.


## <a name="deploy-an-ultra-disk"></a>Een ultra schijf implementeren

# <a name="portal"></a>[Portal](#tab/azure-portal)

In deze sectie wordt beschreven hoe u een virtuele machine implementeert met een ultra schijf als een gegevens schijf. Hierbij wordt ervan uitgegaan dat u bekend bent met het implementeren van een virtuele machine. als dat niet het geval is, raadpleegt u onze [Snelstartgids: een virtuele Windows-machine maken in de Azure Portal](./windows/quick-create-portal.md).

- Meld u aan bij de [Azure Portal](https://portal.azure.com/) en navigeer om een virtuele machine (VM) te implementeren.
- Zorg ervoor dat u een [ondersteunde VM-grootte en-regio](#ga-scope-and-limitations)kiest.
- Selecteer **beschikbaarheids zone** in **beschik bare opties** .
- Vul de resterende items in met de selecties van uw keuze.
- Selecteer **Schijven** .

![Scherm opname van de stroom voor het maken van de VM, Basic-Blade.](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- Selecteer op de Blade schijven de optie **Ja** voor het **inschakelen van ultra Disk-compatibiliteit** .
- Selecteer **maken en koppel een nieuwe schijf** om nu een ultra schijf te koppelen.

![Scherm afbeelding van de stroom voor het maken van een virtuele machine, schijf Blade, Ultra is ingeschakeld en maken en koppelen van een nieuwe schijf wordt gemarkeerd.](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- Voer op de Blade **een nieuwe schijf maken** een naam in en selecteer vervolgens **grootte wijzigen** .

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-create-new-disk-flow.png" alt-text="Scherm opname van de Blade nieuwe schijf maken, gewijzigde grootte gemarkeerd.":::


- Wijzig het **opslag type** naar **Ultra Disk** .
- Wijzig de waarden van **aangepaste schijf grootte (GIB)** , **schijf-IOPS** en **schijf doorvoer** naar keuze.
- Selecteer **OK** in beide Blades.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-select-new-disk.png" alt-text="Scherm opname van de Blade nieuwe schijf maken, gewijzigde grootte gemarkeerd.":::

- Ga verder met de implementatie van de virtuele machine. Dit is dezelfde manier als bij het implementeren van een andere VM.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bepaal eerst welke VM-grootte moet worden geïmplementeerd. Zie de sectie [Ga bereik en beperkingen](#ga-scope-and-limitations) voor een lijst met ondersteunde VM-grootten.

U moet een virtuele machine maken die geschikt is voor het gebruik van ultra disks om een ultra schijf te koppelen.

Vervang of stel de **$vmname** , **$rgname** , **$diskname** , **$Location** , **$Password** **$User** variabelen met uw eigen waarden. Stel **$zone**  in op de waarde van uw beschikbaarheids zone die u aan het [begin van dit artikel](#determine-vm-size-and-region-availability)hebt gekregen. Voer vervolgens de volgende CLI-opdracht uit om een virtuele-VM te maken:

```azurecli-interactive
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bepaal eerst welke VM-grootte moet worden geïmplementeerd. Zie de sectie [Ga bereik en beperkingen](#ga-scope-and-limitations) voor een lijst met ondersteunde VM-grootten.

Als u ultra disks wilt gebruiken, moet u een virtuele machine maken die geschikt is voor het gebruik van ultra Disk-schijven. Vervang of stel de **$ResourceGroup** en **$vmName** variabelen met uw eigen waarden. Stel **$zone** in op de waarde van uw beschikbaarheids zone die u aan het [begin van dit artikel](#determine-vm-size-and-region-availability)hebt gekregen. Voer vervolgens de volgende opdracht uit voor het maken van een virtuele machine met een [nieuwe AzVm](/powershell/module/az.compute/new-azvm) :

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-and-attach-the-disk"></a>De schijf maken en koppelen

Als uw virtuele machine is geïmplementeerd, kunt u een ultra schijf maken en koppelen, het volgende script gebruiken:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="attach-an-ultra-disk"></a>Een ultra schijf koppelen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Als uw bestaande virtuele machine zich in een regio/beschikbaarheids zone bevindt die geschikt is voor het gebruik van ultra schijven, kunt u ook gebruikmaken van ultra schijven zonder dat u een nieuwe virtuele machine hoeft te maken. Door Ultra schijven op uw bestaande virtuele machine in te scha kelen en vervolgens als gegevens schijven te koppelen. Als u ultra Disk-compatibiliteit wilt inschakelen, moet u de virtuele machine stoppen. Nadat u de virtuele machine hebt gestopt, kunt u de compatibiliteit inschakelen en vervolgens de virtuele machine opnieuw opstarten. Zodra compatibiliteit is ingeschakeld, kunt u een ultra schijf koppelen:

- Navigeer naar uw virtuele machine en stop deze, wacht totdat de toewijzing is opheffen.
- Wanneer de toewijzing van de virtuele machine ongedaan is gemaakt, selecteert u **schijven** .
- Selecteer **Bewerken** .

![Scherm opname van een bestaande VM-schijf Blade, bewerken is gemarkeerd.](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Selecteer **Ja** als u **Ultra Disk-compatibiliteit wilt inschakelen** .

![Scherm opname van compatibiliteit met hoge schijven inschakelen.](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Selecteer **Opslaan** .
- Selecteer **gegevens schijf toevoegen** en selecteer in de vervolg keuzelijst voor **naam** de optie **schijf maken** .

![Scherm afbeelding van de Blade schijf, een nieuwe schijf toevoegen.](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Vul een naam in voor de nieuwe schijf en selecteer vervolgens **grootte wijzigen** .
- Wijzig het **account type** in **Ultra Disk** .
- Wijzig de waarden van **aangepaste schijf grootte (GIB)** , **schijf-IOPS** en **schijf doorvoer** naar keuze.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-select-new-disk.png" alt-text="Scherm opname van de Blade nieuwe schijf maken, gewijzigde grootte gemarkeerd.":::

- Selecteer **OK** en selecteer vervolgens **maken** .
- Wanneer u terugkeert naar de Blade van uw schijf, selecteert u **Opslaan** .
- Start de VM opnieuw op.

![Scherm afbeelding van de Blade schijven op de virtuele machine.](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Als uw bestaande virtuele machine zich in een regio/beschikbaarheids zone bevindt die geschikt is voor het gebruik van ultra schijven, kunt u ook gebruikmaken van ultra schijven zonder dat u een nieuwe virtuele machine hoeft te maken.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---cli"></a>Compatibiliteit met hoge schijven inschakelen op een bestaande VM-CLI

Als uw virtuele machine voldoet aan de vereisten die worden beschreven in de stappen [en beperkingen](#ga-scope-and-limitations) van het oog en die zich in de [juiste zone voor uw account](#determine-vm-size-and-region-availability)bevinden, kunt u compatibiliteit met hoge schijven inschakelen op uw virtuele machine.

Als u ultra Disk-compatibiliteit wilt inschakelen, moet u de virtuele machine stoppen. Nadat u de virtuele machine hebt gestopt, kunt u de compatibiliteit inschakelen en vervolgens de virtuele machine opnieuw opstarten. Zodra compatibiliteit is ingeschakeld, kunt u een ultra schijf koppelen:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk---cli"></a>Een ultra schijf-CLI maken

Nu u een virtuele machine hebt die geschikt is voor het koppelen van ultra schijven, kunt u een ultra schijf maken en koppelen.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

### <a name="attach-the-disk---cli"></a>De schijf-CLI koppelen

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als uw bestaande virtuele machine zich in een regio/beschikbaarheids zone bevindt die geschikt is voor het gebruik van ultra schijven, kunt u ook gebruikmaken van ultra schijven zonder dat u een nieuwe virtuele machine hoeft te maken.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---powershell"></a>Compatibiliteit met hoge schijven inschakelen op een bestaande VM-Power shell

Als uw virtuele machine voldoet aan de vereisten die worden beschreven in de stappen [en beperkingen](#ga-scope-and-limitations) van het oog en die zich in de [juiste zone voor uw account](#determine-vm-size-and-region-availability)bevinden, kunt u compatibiliteit met hoge schijven inschakelen op uw virtuele machine.

Als u ultra Disk-compatibiliteit wilt inschakelen, moet u de virtuele machine stoppen. Nadat u de virtuele machine hebt gestopt, kunt u de compatibiliteit inschakelen en vervolgens de virtuele machine opnieuw opstarten. Zodra compatibiliteit is ingeschakeld, kunt u een ultra schijf koppelen:

```azurepowershell
#Stop the VM
Stop-AzVM -Name $vmName -ResourceGroupName $rgName
#Enable ultra disk compatibility
$vm1 = Get-AzVM -name $vmName -ResourceGroupName $rgName
Update-AzVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled $True
#Start the VM
Start-AzVM -Name $vmName -ResourceGroupName $rgName
```

### <a name="create-and-attach-an-ultra-disk---powershell"></a>Een ultra schijf maken en koppelen-Power shell

Nu u een virtuele machine hebt die geschikt is voor het gebruik van ultra schijven, kunt u een ultra schijf maken en koppelen:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="adjust-the-performance-of-an-ultra-disk"></a>De prestaties van een ultra schijf aanpassen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ultra disks bieden een unieke mogelijkheid waarmee u de prestaties ervan kunt aanpassen. U kunt deze aanpassingen aanbrengen vanaf de Azure Portal, op de schijven zelf.

- Navigeer naar uw VM en selecteer **schijven** .
- Selecteer de ultra schijf waarvan u de prestaties wilt wijzigen.

![Scherm afbeelding van de Blade schijven op de virtuele machine is een super schijf gemarkeerd.](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Selecteer **configuratie** en breng vervolgens de gewenste wijzigingen aan.
- Selecteer **Opslaan** .

![Scherm opname van de Blade configuratie op uw Ultra schijf, schijf grootte, IOPS en door Voer zijn gemarkeerd, opslaan is gemarkeerd.](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ultra disks bieden een unieke mogelijkheid waarmee u de prestaties kunt aanpassen. de volgende opdracht ziet u hoe u deze functie kunt gebruiken:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>De prestaties van een ultra schijf aanpassen met behulp van Power shell

Ultra disks hebben een unieke functie waarmee u de prestaties kunt aanpassen, de volgende opdracht is een voor beeld waarmee de prestaties worden aangepast zonder dat de schijf moet worden losgekoppeld:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
---

## <a name="next-steps"></a>Volgende stappen

- [Gebruik Azure Ultra disks in azure Kubernetes service (preview)](../aks/use-ultra-disks.md).
- [De logboek schijf migreren naar een ultra schijf](../azure-sql/virtual-machines/windows/storage-migrate-to-ultradisk.md).
