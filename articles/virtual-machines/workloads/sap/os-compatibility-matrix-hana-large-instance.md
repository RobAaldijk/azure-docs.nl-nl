---
title: Compatibiliteits matrix voor het besturings systeem voor SAP HANA (grote exemplaren) | Microsoft Docs
description: De compatibiliteits matrix staat voor de compatibiliteit van verschillende versies van het besturings systeem met verschillende typen hardware (grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/21/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 14cfd1aef3a49baa11050293fc216256f33cee7a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376660"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Compatibele besturings systemen voor HANA grote instanties

## <a name="hana-large-instance-type-i"></a>Type I van HANA-grote instanties     
  | Besturingssysteem | Beschikbaarheid        | SKU's                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Niet meer aangeboden | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | Beschikbaar           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Beschikbaar           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 12 SP5      | Beschikbaar           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 15 SP1      | Beschikbaar           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | RHEL 7,6         | Beschikbaar           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |

  
### <a name="persistent-memory-skus"></a>Sku's voor permanent geheugen
  | Besturingssysteem | Beschikbaarheid | SKU's                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Beschikbaar    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>HANA grote instantie type II     
  |  Besturingssysteem       | Beschikbaarheid        | SKU's                                                                     |
  |-------------------------|---------------------|--------------------------------------------------------------------------|
  | SLES 12 SP2             | Niet meer aangeboden | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 SP3             | Beschikbaar           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 SP4             | Beschikbaar           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 SP5             | Beschikbaar           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  | SLES 15 SP1             | Beschikbaar           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  | RHEL 7,6                | Beschikbaar           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |

## <a name="related-documents"></a>Gerelateerde documenten

- Meer informatie over [beschik bare sku's](hana-available-skus.md)
- Meer informatie over [het upgraden van het besturings systeem](os-upgrade-hana-large-instance.md)
  

  
  
