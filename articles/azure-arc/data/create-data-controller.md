---
title: Een gegevenscontroller maken
description: Maak een Azure-Arc-gegevens controller op een typisch Kubernetes-cluster met meerdere knoop punten dat u al hebt geïmplementeerd.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6830bdd38e8e1f1d649673cfdaf48e8c50ba6c3e
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413221"
---
# <a name="create-the-azure-arc-data-controller"></a>De Azure Arc-gegevens controller maken

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Overzicht van het maken van de Azure-Arc-gegevens controller

U kunt Azure Arc ingeschakelde gegevens Services maken op meerdere verschillende soorten Kubernetes-clusters en beheerde Kubernetes-Services met meerdere benaderingen.

Momenteel zijn de ondersteunde lijst met Kubernetes-Services en-distributies de volgende:

- Azure Kubernetes Service (AKS)
- Azure Kubernetes service Engine (AKS-Engine) op Azure Stack
- Azure Kubernetes-service op Azure Stack HCI
- Azure RedHat OpenShift (ARO)
- Open Shift container platform (OCP)
- AWS Elastic Kubernetes Service (EKS)
- Google Cloud Kubernetes Engine (GKE)
- Open source, upstream Kubernetes, doorgaans geïmplementeerd met kubeadm

> [!IMPORTANT]
> * De mini maal ondersteunde versie van Kubernetes is v 1.17.
> * De mini maal ondersteunde versie van OCP is 4,3.
> * Bekijk de [connectiviteits vereisten](connectivity.md) om te begrijpen welke connectiviteit vereist is tussen uw omgeving en Azure.
> * Zie de [richt lijnen voor opslag configuratie](storage-configuration.md) voor meer informatie over het configureren van uw permanente opslag.
> * Als u de Azure Kubernetes-service gebruikt, moet de VM-grootte van het werk knooppunt van het cluster ten minste **Standard_D8s_v3** zijn en **Premium-schijven gebruiken.** Het cluster mag niet meerdere beschikbaarheids zones omvatten. 
> * Als u een andere Kubernetes-distributie of-service gebruikt, moet u ervoor zorgen dat u beschikt over een minimale knooppunt grootte van 8 GB RAM en 4 kernen en een totale capaciteit van 32 GB RAM-geheugen beschikbaar is voor al uw Kubernetes knooppunten. U kunt bijvoorbeeld één knoop punt hebben van 32 GB RAM-geheugen en 4 kernen, of u kunt 2 knoop punten hebben met een RAM-geheugen en 4 kernen per generatie.

> [!NOTE]
> Als u het Red Hat open Shift container platform gebruikt in azure, is het raadzaam om de meest recente beschik bare versie te gebruiken.

Afhankelijk van de optie die u kiest, zijn bepaalde hulpprogram ma's _vereist_ , maar u wordt aangeraden [alle client hulpprogramma's te installeren](./install-client-tools.md) voordat u begint met het maken van de Azure Arc-gegevens controller.

Ongeacht de optie die u kiest, moet u tijdens het aanmaak proces de volgende informatie opgeven:

- **Naam gegevens controller** : een beschrijvende naam voor uw gegevens controller, bijvoorbeeld ' productie gegevens controller ', ' Seattle data controller '.
- **Gebruikers naam van de gegevens controller** : de gebruikers naam voor de beheerder van de gegevens controller.
- **Wacht woord voor gegevens controller** : een wacht woord voor de gebruiker van de gegevens controller beheerder.
- **Naam van uw Kubernetes-naam ruimte** : de naam van de Kubernetes-naam ruimte waarin u de gegevens controller wilt maken.
- **Connectiviteits modus** : de [connectiviteits modus](./connectivity.md) van uw cluster. Momenteel wordt alleen indirect ondersteund.
- **Azure-abonnements-id** : de GUID van het Azure-abonnement waarvoor u de gegevens controller bron in azure wilt maken.
- **Naam van de Azure-resource groep** : de naam van de resource groep waar u de gegevens controller bron in azure wilt maken.
- **Azure-locatie** : de Azure-locatie waar de meta gegevens van de resource controller bron worden opgeslagen in Azure. Zie [globale Azure-infra structuur/-producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)voor een lijst met beschik bare regio's.

## <a name="next-steps"></a>Volgende stappen

Er zijn meerdere opties voor het maken van de Azure Arc-gegevens controller:

> **Wilt u gewoon iets uitproberen?**  
> Ga snel aan de slag met [Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) direct op Azure Kubernetes service (AKS), AWS elastische Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) of een virtuele machine van Azure.
> 
- [Een gegevens controller maken met [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](create-data-controller-using-azdata.md)
- [Een gegevens controller maken met Azure Data Studio](create-data-controller-azure-data-studio.md)
- [Een gegevens controller maken van de Azure Portal via een Jupyter-notebook in Azure Data Studio](create-data-controller-resource-in-azure-portal.md)
- [Een gegevens controller maken met Kubernetes-hulpprogram ma's zoals kubectl of OC](create-data-controller-using-kubernetes-native-tools.md)
- [Een gegevens controller maken met Azure Arc aan de slag voor een versnelde ervaring van een test implementatie](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services)
