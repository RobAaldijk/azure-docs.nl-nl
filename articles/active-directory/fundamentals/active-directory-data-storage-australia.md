---
title: Identiteits gegevens opslag voor Australische en Nieuw-Zeelandse klanten-Azure AD
description: Meer informatie over waar Azure Active Directory identiteits gegevens opslaat voor de Australische en Nieuw-Zeelandse klanten.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7ed1d4c49c46673f1a1c2b5cb08b2467490acae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89565116"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Identiteits gegevens opslag voor Australische en Nieuw-Zeelandse klanten in Azure Active Directory

Identiteits gegevens worden opgeslagen door Azure AD op een geografische locatie op basis van het adres van uw organisatie bij het abonneren op een micro soft online service, zoals Microsoft 365 en Azure. Voor informatie over waar uw identiteits klant gegevens zijn opgeslagen, kunt u de sectie [waar bevinden zich uw gegevens?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) in het micro soft vertrouwens centrum.

> [!NOTE]
> Services en toepassingen die met Azure AD worden geïntegreerd, hebben toegang tot klant gegevens van de identiteit. Evalueer elke service en toepassing die u gebruikt om te bepalen hoe klant gegevens van de identiteit worden verwerkt door die specifieke service en toepassing, en of ze voldoen aan de vereisten voor gegevens opslag van uw bedrijf. Zie de sectie Waar bevinden uw gegevens zich? van het Microsoft Trust Center voor meer informatie over de gegevenslocatie van Microsoft-services.

Voor klanten die een adres hebben ingevoerd in Australië of Nieuw-Zeeland, houdt Azure AD identiteits gegevens voor deze services in Australische data centers: 
- Azure AD-adreslijst beheer 
- Verificatie

Alle andere Azure AD-Services slaan klant gegevens op in wereld wijde data centers. Als u het Data Center voor een service wilt zoeken, raadpleegt u [Azure Active Directory – waar bevinden zich uw gegevens?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Multi-factor Authentication (MFA) Microsoft Azure

Met MFA worden identiteits gegevens van klanten opgeslagen in wereld wijde data centers. Zie [azure multi-factor Authentication verzameling van gebruikers gegevens](../authentication/concept-mfa-data-residency.md)voor meer informatie over de gebruikers gegevens die worden verzameld en opgeslagen door Azure MFA en Azure MFA-server op basis van de Cloud.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over de functies en functionaliteit die hierboven worden beschreven:
- [Wat is Multi-Factor Authentication?](../authentication/concept-mfa-howitworks.md)