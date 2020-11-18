---
title: 'Zelfstudie: Een NSX-T-netwerksegment toevoegen in Azure VMware Solution'
description: Meer informatie over hoe u een NSX-T-netwerksegment kunt maken om virtuele machines (VM's) te gebruiken in vCenter.
ms.topic: tutorial
ms.date: 11/09/2020
ms.openlocfilehash: 8ecb37a42e2986bd1c6261b8fe6c23382323b31d
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335044"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>Zelfstudie: Een netwerksegment toevoegen in Azure VMware Solution 

De virtuele machines (VM's) die in vCenter zijn gemaakt, worden geplaatst op de netwerksegmenten die in NSX-T zijn gemaakt. Deze zijn zichtbaar in vCenter.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Navigeren in NSX-T Manager om netwerksegmenten toe te voegen
> * Een nieuw netwerksegment toevoegen
> * Het nieuwe netwerksegment observeren in vCenter

## <a name="prerequisites"></a>Vereisten

Een privécloud van Azure VMware Solution met toegang tot de vCenter- en NSX-T Manager-interfaces. Zie de zelfstudie [Netwerken configureren](tutorial-configure-networking.md) voor meer informatie.

## <a name="add-a-network-segment"></a>Een netwerksegment toevoegen

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een NSX-T-netwerksegmenten gemaakt om te gebruiken voor VM's in vCenter. 

U kunt nu het volgende doen: 

- [DHCP voor Azure VMware Solution maken en beheren](manage-dhcp.md)
- [Een inhoudsbibliotheek maken voor het implementeren van VM's in Azure VMware Solution](deploy-vm-content-library.md) 
- [On-premises omgevingen peeren met een privécloud](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
