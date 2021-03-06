---
title: Verbindings monitor maken-Power shell
titleSuffix: Azure Network Watcher
description: Meer informatie over het maken van een verbindings monitor met behulp van Power shell.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: 532f045233f26a9a2933a19ae7a0a893195ad33f
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94384101"
---
# <a name="create-a-connection-monitor-using-powershell"></a>Een verbindings monitor maken met behulp van Power shell

Meer informatie over het maken van een verbindings monitor om de communicatie tussen uw resources te bewaken met behulp van Power shell.

## <a name="before-you-begin"></a>Voordat u begint 

In verbindings monitors die u maakt in verbindings beheer, kunt u on-premises machines en Azure-Vm's als bronnen toevoegen. Deze verbindings monitors kunnen ook de connectiviteit met eind punten bewaken. De eind punten kunnen zich op Azure of een andere URL of een ander IP-adres bevindt.

De verbindings monitor bevat de volgende entiteiten:

* **Bron van de verbindings monitor** : een regio-specifieke Azure-resource. Alle volgende entiteiten zijn eigenschappen van een verbindingscontrole resource.
* **Eind punt** – een bron of doel die deel uitmaakt van connectiviteits controles. Voor beelden van eind punten zijn Azure-Vm's, on-premises agents, Url's en Ip's.
* **Test configuratie** : een protocol-specifieke configuratie voor een test. Op basis van het protocol dat u hebt gekozen, kunt u de poort, drempel waarden, test frequentie en andere para meters definiëren.
* **Test groep** : de groep die bron-eind punten, bestemmings eindpunten en test configuraties bevat. Een verbindings monitor kan meer dan één test groep bevatten.
* **Testen** : de combi natie van een bron eindpunt, bestemmings eindpunt en test configuratie. Een test is het meest gedetailleerde niveau waarmee bewakings gegevens beschikbaar zijn. De bewakings gegevens omvatten het percentage controles dat is mislukt en de round-trip tijd (RTT).

    ![Diagram van een verbindings monitor, waarbij de relatie tussen test groepen en tests wordt gedefinieerd](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-powershell"></a>Stappen om te maken met Power shell

Gebruik de volgende opdrachten om een verbindings monitor te maken met behulp van Power shell.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Beschrijving van eigenschappen

* connectionMonitorName: naam van de bron van de verbindings monitor

* Subabonnement-ID van het abonnement waarvoor u de verbindings monitor wilt maken

* NW-Network Watcher Resource-ID waarin CM wordt gemaakt 

* locatie-regio waarin de verbindings monitor wordt gemaakt

* Eindpunten
    * naam: unieke naam voor elk eind punt
    * resourceId: voor Azure-eind punten verwijst de resource-ID naar de Azure Resource Manager Resource-ID voor virtuele machines. Voor niet-Azure-eind punten verwijst Resource-ID naar de resource-ID van de Azure Resource Manager voor de Log Analytics werk ruimte die is gekoppeld aan niet-Azure-agents.
    * adres: alleen van toepassing als de resource-ID niet is opgegeven of als de resource-ID Log Analytics werk ruimte is. Als dit wordt gebruikt in combi natie met Log Analytics Resource-ID, verwijst dit naar de FQDN van de agent die kan worden gebruikt voor de bewaking. Als u zonder Resource-ID gebruikt, kan dit de URL of het IP-adres van een openbaar eind punt zijn.
    * filter: voor niet-Azure-eind punten gebruikt u filter om agents te selecteren uit Log Analytics werk ruimte die wordt gebruikt voor de bewaking van de bron van de verbindings monitor. Als er geen filters zijn ingesteld, kunnen alle agents die deel uitmaken van de Log Analytics-werk ruimte worden gebruikt voor de bewaking
        * type: Stel type in als agent adres
        * adres: Stel adres in als de FQDN van uw on-premises agent

* Test groepen
    * name: naam van de test groep.
    * testConfigurations-test configuraties op basis waarvan de bron-eind punten verbinding maken met doel eindpunten
    * bronnen: Kies uit de hierboven gemaakte eind punten. Op Azure-bron eindpunten moet Azure Network Watcher-extensie zijn geïnstalleerd en op niet-Azure gebaseerde bron-eind punten moet Log Analytics agent zijn geïnstalleerd. Zie [bewakings agenten installeren](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)voor informatie over het installeren van een agent voor uw bron.
    * doelen: Kies uit de hierboven gemaakte eind punten. U kunt de verbinding met virtuele machines van Azure of een eind punt (een openbaar IP-adres, URL of FQDN) bewaken door ze als bestemming op te geven. In één test groep kunt u Azure-Vm's, Office 365 Url's, Dynamics 365-Url's en aangepaste eind punten toevoegen.
    * uitschakelen: gebruik dit veld om de bewaking uit te scha kelen voor alle bronnen en doelen die door de test groep worden opgegeven.

* Test configuraties
    * naam: naam van de test configuratie.
    * testFrequencySec: Geef op hoe vaak bronnen worden gepingd op het protocol en de poort die u hebt opgegeven. U kunt kiezen uit 30 seconden, 1 minuut, 5 minuten, 15 minuten of 30 minuten. Bronnen testen de connectiviteit met bestemmingen op basis van de waarde die u kiest. Als u bijvoorbeeld 30 seconden selecteert, wordt de verbinding met de bestemming ten minste eenmaal in een periode van 30 seconden gecontroleerd.
    * Protocol: u kunt TCP, ICMP, HTTP of HTTPS kiezen. Afhankelijk van het protocol kunt u bepaalde configuraties van specifieke protocollen uitvoeren
        * preferHTTPS: Geef op of HTTPS via HTTP moet worden gebruikt
        * poort: Geef de doel poort van uw keuze op.
        * disableTraceRoute: dit is van toepassing op test groepen waarvan het protocol TCP of ICMP is. Het stoppen van bronnen van het detecteren van topologie en hop-by-Hop RTT wordt gestopt.
    * successThreshold-u kunt drempels instellen voor de volgende netwerk parameters:
        * checksFailedPercent: Stel het percentage controles in dat kan mislukken wanneer bronnen de connectiviteit met bestemmingen controleren met behulp van de criteria die u hebt opgegeven. Voor het TCP-of ICMP-protocol kan het percentage mislukte controles worden vergeleken met het percentage pakket verlies. Voor het HTTP-protocol vertegenwoordigt dit veld het percentage HTTP-aanvragen dat geen antwoord heeft ontvangen.
        * roundTripTimeMs: Stel de RTT in milliseconden in om te bepalen hoe lang bronnen kunnen worden verbonden met het doel via de test configuratie.

## <a name="scale-limits"></a>Schaal limieten

Verbindings monitors hebben de volgende schaal limieten:

* Maximum aantal verbindings monitors per abonnement per regio: 100
* Maximum aantal test groepen per verbindings monitor: 20
* Maximum aantal bronnen en bestemmingen per verbindings monitor: 100
* Maximum aantal test configuraties per verbindings monitor: 20

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het analyseren van bewakings gegevens en het instellen van waarschuwingen](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* Meer informatie [over het vaststellen van problemen in uw netwerk](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
