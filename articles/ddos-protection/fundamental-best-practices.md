---
title: Aanbevolen procedures voor Azure DDoS Protection
description: Meer informatie over de aanbevolen beveiligings procedures voor het gebruik van DDoS-beveiliging.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 594a7e2a6977cc2a7052a15e1a007c68c08da259
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905172"
---
# <a name="fundamental-best-practices"></a>Best practices voor de basis

De volgende secties bieden prescriptieve richt lijnen voor het bouwen van DDoS-flexibele services in Azure.

## <a name="design-for-security"></a>Ontwerpen voor beveiliging

Zorg ervoor dat beveiliging een prioriteit heeft gedurende de hele levens cyclus van een toepassing, van ontwerp en implementatie tot implementatie en bewerkingen. Toepassingen kunnen bugs hebben die een relatief lage hoeveelheid aanvragen mogelijk maken voor het gebruik van een onordinaat aantal resources, wat resulteert in een service storing. 

Als u een service die wordt uitgevoerd op Microsoft Azure wilt beveiligen, moet u een goed beeld hebben van de architectuur van uw toepassing en zich richten op de [vijf pijlers van de software kwaliteit](/azure/architecture/guide/pillars).
U moet rekening houden met typische verkeers volumes, het verbindings model tussen de toepassing en andere toepassingen en de service-eind punten die beschikbaar worden gesteld aan het open bare Internet.

Ervoor zorgen dat een toepassing robuust genoeg is voor het afhandelen van een denial of service die is gericht op de toepassing zelf, is het belangrijkst. Beveiliging en privacy zijn ingebouwd in het Azure-platform, te beginnen met de [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). De SDL vertrouwt de beveiliging van elke ontwikkelings fase en zorgt ervoor dat Azure voortdurend wordt bijgewerkt om het nog veiliger te maken.

## <a name="design-for-scalability"></a>Ontwerpen voor schaal baarheid

Schaal baarheid is hoe goed een systeem een grotere belasting kan afhandelen. Ontwerp uw toepassingen zodanig dat ze [horizon taal kunnen worden geschaald](/azure/architecture/guide/design-principles/scale-out) om te voldoen aan de vraag naar een versterkte belasting, met name in het geval van een DDoS-aanval. Als uw toepassing afhankelijk is van één exemplaar van een service, wordt er een Single Point of Failure gemaakt. Door meerdere exemplaren in te richten, zorgt u ervoor dat uw systeem robuuster en schaalbaar is.

Selecteer voor [Azure app service](/azure/app-service/app-service-value-prop-what-is)een [app service-abonnement](/azure/app-service/overview-hosting-plans) dat meerdere exemplaren biedt. Configureer voor Azure Cloud Services elk van uw rollen om [meerdere exemplaren](/azure/cloud-services/cloud-services-choose-me)te gebruiken. Zorg ervoor dat de architectuur van de virtuele machine (VM) voor [Azure virtual machines](../virtual-machines/index.yml)meer dan één VM bevat en dat elke VM is opgenomen in een [beschikbaarheidsset](../virtual-machines/windows/tutorial-availability-sets.md). We raden u aan [virtuele-machine schaal sets](../virtual-machine-scale-sets/overview.md) te gebruiken voor de mogelijkheden voor automatisch schalen.

## <a name="defense-in-depth"></a>Diepgaande verdediging

Het voor beeld van een diep gaande verdedigings linie is het beheren van Risico's met behulp van verschillende verdedigings strategieën. Als u beveiligings beveiliging in een toepassing laagt, vermindert u de kans op een geslaagde aanval. U wordt aangeraden beveiligde ontwerpen voor uw toepassingen te implementeren met behulp van de ingebouwde mogelijkheden van het Azure-platform.

Het risico van een aanval neemt bijvoorbeeld toe met de grootte ( *Surface Area* ) van de toepassing. U kunt de surface area verminderen door een goedkeurings lijst te gebruiken om de beschik bare IP-adres ruimte te sluiten en poorten te belui Steren die niet nodig zijn op de load balancers ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) en [Azure-toepassing gateway](/azure/application-gateway/application-gateway-create-probe-portal)). [Netwerk beveiligings groepen (nsg's)](/azure/virtual-network/security-overview) zijn een andere manier om de kwets baarheid voor aanvallen te verminderen.
U kunt [service Tags](/azure/virtual-network/security-overview#service-tags) en [toepassings beveiligings groepen](/azure/virtual-network/security-overview#application-security-groups) gebruiken om de complexiteit te minimaliseren voor het maken van beveiligings regels en het configureren van netwerk beveiliging, als een natuurlijke uitbrei ding van de structuur van een toepassing.

Indien mogelijk moet u Azure-Services in een [virtueel netwerk](/azure/virtual-network/virtual-networks-overview) implementeren. Met deze procedure kunnen service resources communiceren via privé-IP-adressen. Azure service-verkeer van een virtueel netwerk maakt standaard gebruik van open bare IP-adressen als bron-IP-adressen. Als u [service-eind punten](/azure/virtual-network/virtual-network-service-endpoints-overview) gebruikt, wordt service verkeer voor het gebruik van privé adressen van het virtuele netwerk geswitcheerd als de bron-IP-adressen wanneer ze toegang krijgen tot de Azure-service vanuit een virtueel netwerk.

Vaak zien we de on-premises resources van klanten die zijn aangevallen samen met hun resources in Azure. Als u een on-premises omgeving verbindt met Azure, raden we u aan om de bloot stelling van on-premises resources naar het open bare Internet te minimaliseren. U kunt de functies Scale en Advanced DDoS Protection van Azure gebruiken door uw bekende open bare entiteiten in azure te implementeren. Omdat deze openbaar toegankelijke entiteiten vaak een doel zijn voor DDoS-aanvallen, wordt de impact op uw on-premises resources gereduceerd door ze in azure te brengen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een DDoS-beschermings plan](manage-ddos-protection.md).