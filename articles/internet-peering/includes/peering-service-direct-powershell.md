---
title: bestand opnemen
titleSuffix: Azure
description: bestand opnemen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 526d8a6a103e7623bac459004bf9ac79e4927541
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81686993"
---
1. Verbindingen weer geven voor de geselecteerde directe peering.
    ```powershell
    $directPeering.Connections

    PeeringDBFacilityId         : 6
    UseForPeeringService        : False
    SessionAddressProvider      : Peer
    SessionPrefixV4             : 12.245.179.20/30
    ConnectionIdentifier        : 3f196dfe-c174-4900-ab1c-fb7b50ad1535
    SessionPrefixV6             :
    MicrosoftSessionIPv4Address : 12.245.179.22
    BandwidthInMbps             : 100000
    SessionStateV4              : Established
    SessionStateV6              : None
    ConnectionState             : Active
    ```
1. Selecteer de verbinding die u wilt inschakelen voor de peering-service. In dit voor beeld gebruiken we de enige verbinding die beschikbaar is.
    ```powershell
    $directPeering.Connections[1] = $directPeering.Connections[1] | Set-AzPeeringDirectConnectionObject -UseForPeeringService $true

    PeeringDBFacilityId         : 6
    UseForPeeringService        : True
    SessionAddressProvider      : Peer
    SessionPrefixV4             : 12.245.179.20/30
    ConnectionIdentifier        : 3f196dfe-c174-4900-ab1c-fb7b50ad1535
    SessionPrefixV6             :
    MicrosoftSessionIPv4Address : 12.245.179.22
    BandwidthInMbps             : 100000
    SessionStateV4              : Established
    SessionStateV6              : None
    ConnectionState             : Active
    ```
1. Sla de wijzigingen die zijn aangebracht in de directe peering nu op met behulp van deze opdracht:
    ```powershell
    $directPeering | Update-AzPeering
    ```
    
    Hier volgt een voor beeld van uitvoer:
    
    ```powershell
        Name                 : SeattleDirectPeering
        Sku.Name             : Basic_Premium_Free
        Kind                 : Direct
        Connections          : {71,71}
        PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/SeattleDirectPeering
        UseForPeeringService : True
        PeeringLocation      : Seattle
        ProvisioningState    : Succeeded
        Location             : centralus
        Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
        Type                 : Microsoft.Peering/peerings
        Tags                 : {}
    ```
    