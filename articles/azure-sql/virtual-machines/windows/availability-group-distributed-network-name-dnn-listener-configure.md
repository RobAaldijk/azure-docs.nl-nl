---
title: DNN-listener voor de beschikbaarheids groep configureren
description: Meer informatie over het configureren van een DNN-listener (Distributed Network name) voor het vervangen van de VNN-listener van uw virtuele netwerk naam en het routeren van verkeer naar uw AlwaysOn-beschikbaarheids groep op SQL Server op Azure VM.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: abfcd6a13bc5e8ad262fe47111eb680ad00a34df
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168874"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>Een DNN-listener configureren voor een beschikbaarheids groep
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Met SQL Server op virtuele Azure-machines stuurt de gedistribueerde netwerk naam (DNN) verkeer naar de juiste geclusterde bron. Het biedt een eenvoudige manier om verbinding te maken met een AlwaysOn-beschikbaarheids groep (AG) dan de naam van het virtuele netwerk (VNN), zonder dat er een Azure Load Balancer nodig is. 

In dit artikel leert u hoe u een DNN-listener kunt configureren om de VNN-listener te vervangen en verkeer te routeren naar uw beschikbaarheids groep met SQL Server op Azure-Vm's voor hoge Beschik baarheid en herstel na nood gevallen (HADR). 

De functie DNN listener is momenteel alleen beschikbaar vanaf SQL Server 2019 CU8 op Windows Server 2016 en hoger. 

Voor een alternatieve connectiviteits optie kunt u in plaats daarvan een [VNN-listener en Azure Load Balancer](availability-group-vnn-azure-load-balancer-configure.md) gebruiken. 

## <a name="overview"></a>Overzicht

Een DNN-listener (Distributed Network name) vervangt de traditionele listener voor de beschikbaarheids groep van de VNN (Virtual Network name) wanneer deze wordt gebruikt met AlwaysOn- [beschikbaarheids groepen op SQL Server vm's](availability-group-overview.md). Dit leidt tot een Azure Load Balancer om verkeer te routeren, de implementatie, het onderhoud en het verbeteren van de failover te vereenvoudigen. 

Gebruik de DNN-listener voor het vervangen van een bestaande VNN-listener, of gebruik deze in combi natie met een bestaande VNN-listener, zodat uw beschikbaarheids groep twee afzonderlijke verbindings punten heeft: een met de naam van de VNN-listener (en poort als niet-standaard) en één met de naam en poort van de DNN-listener. 

## <a name="prerequisites"></a>Vereisten

Voordat u de stappen in dit artikel hebt voltooid, hebt u het volgende nodig:

- SQL Server 2019 op CU8 of hoger, op Windows Server 2016 en hoger
- Heeft besloten dat de naam van het gedistribueerde netwerk de juiste [connectiviteits optie is voor uw HADR-oplossing](hadr-cluster-best-practices.md#connectivity).
- De AlwaysOn- [beschikbaarheids groep](availability-group-overview.md)is geconfigureerd. 
- De nieuwste versie van [Power shell](/powershell/azure/install-az-ps)is geïnstalleerd. 


## <a name="create-script"></a>Script maken

Gebruik Power shell om de DNN-resource (Distributed Network name) te maken en deze te koppelen aan uw beschikbaarheids groep. 

Voer hiervoor de volgende stappen uit: 

1. Open een teksteditor zoals Kladblok. 
1. Kopieer en plak het volgende script: 

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. Sla het script op als een `.ps1` bestand, zoals `add_dnn_listener.ps1` . 


## <a name="execute-script"></a>Script uitvoeren

Als u de DNN-listener wilt maken, voert u het script uit dat is door gegeven in para meters voor de naam van de beschikbaarheids groep, naam van de listener en poort. 

Als u bijvoorbeeld de naam van de beschikbaarheids groep van `ag1` , de naam van de listener en de listener-poort wilt aannemen `dnnlsnr` `6789` , voert u de volgende stappen uit: 

1. Open een opdracht regel interface programma, zoals opdracht prompt of Power shell. 
1. Ga naar de locatie waar u het script hebt opgeslagen `.ps1` , zoals c:\Documents. 
1. Voer het script uit: ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` . Bijvoorbeeld: 

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>Listener controleren

Gebruik SQL Server Management Studio of Transact-SQL om te bevestigen dat uw DNN-listener is gemaakt. 

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Vouw de listeners van de **beschikbaarheids groep** in [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) uit om uw DNN-listener te bekijken: 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="Bekijk de DNN-listener onder beschikbaarheids groep-listeners in SQL Server Management Studio (SSMS)":::

### <a name="transact-sql"></a>Transact-SQL

Gebruik Transact-SQL om de status van de DNN-listener weer te geven: 

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

Een waarde van `1` voor `is_distributed_network_name` geeft aan dat de listener een gedistribueerde netwerk naam (DNN) is: 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="Bekijk de DNN-listener onder beschikbaarheids groep-listeners in SQL Server Management Studio (SSMS)":::


## <a name="update-connection-string"></a>connection string bijwerken

Verbindings reeksen voor toepassingen bijwerken zodat ze verbinding maken met de DNN-listener. Als u wilt zorgen voor snelle connectiviteit bij failover, voegt `MultiSubnetFailover=True` u toe aan de Connection String als de SQL-Client dit ondersteunt. 

## <a name="test-failover"></a>Testfailover

Test de failover van de beschikbaarheids groep om de functionaliteit te controleren. 

Voer de volgende stappen uit om de failover te testen: 

1. Maak verbinding met de DNN-listener of een van de replica's met behulp van [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
1. Vouw AlwaysOn- **beschikbaarheids groep** uit in **objectverkenner**. 
1. Klik met de rechter muisknop op de beschikbaarheids groep en kies **failover** om de **wizard Failover**te openen. 
1. Volg de aanwijzingen voor het kiezen van een failover-doel en het mislukken van de beschikbaarheids groep naar een secundaire replica. 
1. Controleer of de data base is gesynchroniseerd met de status van de nieuwe primaire replica. 
1. Beschrijving Een failback uitvoeren naar de oorspronkelijke primaire of een andere secundaire replica. 

## <a name="test-connectivity"></a>Connectiviteit testen

Test de verbinding met uw DNN-listener met de volgende stappen:

1. Open [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
1. Maak verbinding met uw DNN-listener. 
1. Open een nieuw query venster en controleer de replica waarmee u bent verbonden door uit te voeren `SELECT @@SERVERNAME` . 
1. Failover van de beschikbaarheids groep naar een andere replica.
1. Na een redelijke hoeveelheid tijd voert u uit `SELECT @@SERVERNAME` om te bevestigen dat uw beschikbaarheids groep nu wordt gehost op een andere replica. 


## <a name="limitations"></a>Beperkingen

- Momenteel wordt een DNN-listener voor een beschikbaarheids groep alleen ondersteund voor SQL Server 2019 CU8 en hoger op Windows Server 2016 en hoger. 
- Er zijn mogelijk extra overwegingen bij het werken met andere SQL Server functies en een beschikbaarheids groep met een DNN. Zie voor meer informatie [AG met DNN-interoperabiliteit](availability-group-dnn-interoperability.md). 

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over SQL Server-HADR-functies in azure [beschikbaarheids groepen](availability-group-overview.md) en een [failover-cluster exemplaar](failover-cluster-instance-overview.md). U kunt ook de [Aanbevolen procedures](hadr-cluster-best-practices.md) voor het configureren van uw omgeving voor hoge Beschik baarheid en herstel na nood gevallen leren. 


