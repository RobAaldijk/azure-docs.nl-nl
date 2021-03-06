---
title: Aanbevolen procedures voor Azure automanage voor Virtual Machines
description: Meer informatie over de aanbevolen procedures voor het beheren van Azure voor virtuele machines voor services die automatisch worden uitgevoerd en voor u zijn geconfigureerd.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: 2b8009d6d571a9daf7163d2126b6d52edfc4bfdd
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521081"
---
# <a name="azure-automanage-for-virtual-machines-best-practices"></a>Aanbevolen procedures voor Azure automanage voor virtuele machines


Deze Azure-Services worden automatisch voor u uitgevoerd wanneer u automanage voor virtuele machines gebruikt. Ze zijn essentieel voor het technische document van best practices, dat u kunt vinden in ons [Cloud adoptie Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

Voor al deze services worden automatisch de voor bereid, automatisch geconfigureerd, bewaken, controleren en verholpen als er een drift wordt gedetecteerd. Zie [Azure automanage voor virtuele machines voor](automanage-virtual-machines.md)meer informatie over dit proces.


## <a name="participating-services"></a>Deelnemende Services

|Service    |Beschrijving    |Ondersteunde profielen<sup>1</sup>    |Voor keuren ondersteund<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|Bewaking van VM Insights    |Azure Monitor voor VM's bewaakt de prestaties en status van uw virtuele machines, met inbegrip van de actieve processen en afhankelijkheden van andere bronnen. [Meer](../azure-monitor/insights/vminsights-overview.md)informatie.    |Aanbevolen procedures voor Azure VM-productie    |Nee    |
|Backup    |Azure Backup biedt onafhankelijke en geïsoleerde back-ups om bescherming te bieden tegen onbedoelde vernietiging van de gegevens op uw VM's. [Meer](../backup/backup-azure-vms-introduction.md)informatie. De kosten zijn gebaseerd op het aantal en de grootte van virtuele machines die worden beveiligd. [Meer](https://azure.microsoft.com/pricing/details/backup/)informatie.    |Aanbevolen procedures voor Azure VM-productie    |Ja    |
|Azure Security Center    |Azure Security Center is een systeem voor geïntegreerde infrastructuur beveiliging dat de beveiligings postuur van uw data centers versterkt en geavanceerde bedreigings beveiliging biedt voor uw hybride werk belastingen in de Cloud. [Meer](../security-center/security-center-introduction.md)informatie.  Met automatisch beheer configureert u het abonnement waar uw VM zich bevindt in de gratis laag van Azure Security Center. Als uw abonnement al is gestart naar Azure Security Center, wordt dit niet opnieuw geconfigureerd door automatisch beheer.    |Aanbevolen procedures voor Azure VM – productie, aanbevolen procedures voor Azure-VM'S – dev/test    |Nee    |
|Microsoft Antimalware    |Micro soft antimalware voor Azure is een gratis real-time beveiliging waarmee virussen, spyware en andere schadelijke software kunnen worden geïdentificeerd en verwijderd. Er worden waarschuwingen gegenereerd wanneer bekende schadelijke of ongewenste software probeert zichzelf te installeren of uit te voeren op uw Azure-systemen. [Meer](../security/fundamentals/antimalware.md)informatie. |Aanbevolen procedures voor Azure VM – productie, aanbevolen procedures voor Azure-VM'S – dev/test    |Ja    |
|Updatebeheer    |U kunt Updatebeheer in Azure Automation gebruiken om updates van het besturings systeem voor uw virtuele machines te beheren. U kunt snel de status van beschik bare updates op alle agent computers beoordelen en het proces voor het installeren van vereiste updates voor servers beheren. [Meer](../automation/update-management/update-mgmt-overview.md)informatie.    |Aanbevolen procedures voor Azure VM – productie, aanbevolen procedures voor Azure-VM'S – dev/test    |Nee    |
|Wijzigingen bijhouden &-inventarisatie    |Wijzigingen bijhouden en Inventaris combineren functies voor het bijhouden van wijzigingen en inventaris, zodat u wijzigingen in de infrastructuur van virtuele machines en servers kunt bijhouden. De service biedt ondersteuning voor het bijhouden van wijzigingen in Services, daemons software, REGI ster en bestanden in uw omgeving om u te helpen bij het vaststellen van ongewenste wijzigingen en het activeren van waarschuwingen. Met inventarisondersteuning kunt u in-guest resources opvragen voor zichtbaarheid van de geïnstalleerde toepassingen en andere configuratie-items.  [Meer](../automation/change-tracking/overview.md)informatie.    |Aanbevolen procedures voor Azure VM – productie, aanbevolen procedures voor Azure-VM'S – dev/test    |Nee    |
|Azure Automation Account    |Azure Automation ondersteunt beheer gedurende de gehele levenscyclus van uw infrastructuur en toepassingen. [Meer](../automation/automation-intro.md)informatie.    |Aanbevolen procedures voor Azure VM – productie, aanbevolen procedures voor Azure-VM'S – dev/test    |Nee    |
|Log Analytics-werkruimte    |Azure Monitor worden logboek gegevens opgeslagen in een Log Analytics-werk ruimte, een Azure-resource en een container waarin gegevens worden verzameld, geaggregeerd en fungeert als een administratieve grens. [Meer](../azure-monitor/platform/design-logs-deployment.md)informatie.    |Aanbevolen procedures voor Azure VM – productie, aanbevolen procedures voor Azure-VM'S – dev/test    |Nee    |


Er zijn <sup>1</sup> configuratie profielen beschikbaar wanneer u automatische beheer inschakelt. [Meer](automanage-virtual-machines.md#configuration-profiles)informatie. U kunt ook de standaard instellingen van het configuratie profiel aanpassen en uw eigen voor keuren instellen in de beperkingen van de best practices.


## <a name="next-steps"></a>Volgende stappen

Probeer automanage in te scha kelen voor virtuele machines in de Azure Portal.

> [!div class="nextstepaction"]
> [Schakel automanage in voor virtuele machines in de Azure Portal](quick-create-virtual-machines-portal.md)