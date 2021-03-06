---
title: Rollen weer geven die zijn toegewezen aan een groep in Azure Active Directory | Microsoft Docs
description: Meer informatie over hoe de functies die aan een groep zijn toegewezen, kunnen worden weer gegeven met behulp van het Azure AD-beheer centrum. Het weer geven van groepen en toegewezen rollen zijn standaard gebruikers machtigingen.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8fdfd99b2ef5c1578bc9e87534461d68398c9f6
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378275"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Rollen weer geven die zijn toegewezen aan een groep in Azure Active Directory

In deze sectie wordt beschreven hoe de functies die aan een groep zijn toegewezen, kunnen worden weer gegeven met behulp van het Azure AD-beheer centrum. Het weer geven van groepen en toegewezen rollen zijn standaard gebruikers machtigingen.

1. Meld u aan bij het [Azure AD-beheer centrum](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) met alle niet-beheerders-of beheerders referenties.

1. Selecteer de groep waarin u geïnteresseerd bent.

1. Selecteer **toegewezen rollen**. U kunt nu alle Azure AD-rollen zien die aan deze groep zijn toegewezen.

   ![Alle rollen weer geven die zijn toegewezen aan een geselecteerde groep](./media/groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>PowerShell gebruiken

### <a name="get-object-id-of-the-group"></a>Object-ID van de groep ophalen

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>Roltoewijzing weer geven aan een groep

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph-API gebruiken

### <a name="get-object-id-of-the-group"></a>Object-ID van de groep ophalen

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>Roltoewijzingen voor een groep ophalen

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Volgende stappen

- [Cloudgroepen gebruiken om roltoewijzingen te beheren](groups-concept.md)
- [Problemen met rollen die zijn toegewezen aan cloudgroepen oplossen](groups-faq-troubleshooting.md)
