---
title: Ingebouwde apps publiceren in virtueel bureau blad van Windows-Azure
description: Het publiceren van ingebouwde apps in Windows virtueel bureau blad.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b088f1f58e26dca854c17b0765607ebe76017dfe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009491"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Ingebouwde apps publiceren in virtueel bureau blad van Windows

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/publish-apps-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

In dit artikel wordt uitgelegd hoe u apps publiceert in uw virtuele Windows-desktop omgeving.

## <a name="publish-built-in-apps"></a>Ingebouwde apps publiceren

Een ingebouwde app publiceren:

1. Maak verbinding met een van de virtuele machines in uw hostgroep.
2. Volg de instructies in [dit artikel](/powershell/module/appx/get-appxpackage?view=win10-ps/)om de **PackageFamilyName** op te halen van de app die u wilt publiceren.
3. Voer ten slotte de volgende cmdlet uit met `<PackageFamilyName>` vervangen door de **PackageFamilyName** die u in de vorige stap hebt gevonden:

   ```powershell
   New-AzWvdApplication -Name <applicationname> -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -FilePath "shell:appsFolder\<PackageFamilyName>!App" -CommandLineSetting <Allow|Require|DoNotAllow> -IconIndex 0 -IconPath <iconpath> -ShowInPortal:$true
   ```

>[!NOTE]
> Windows virtueel bureau blad ondersteunt alleen het publiceren van apps met installatie locaties die beginnen met `C:\Program Files\WindowsApps` .

## <a name="update-app-icons"></a>App-pictogrammen bijwerken

Nadat u een app hebt gepubliceerd, heeft deze het standaard pictogram van de Windows-app in plaats van de normale pictogram afbeelding. Als u het pictogram wilt wijzigen in het gewone pictogram, plaatst u de afbeelding van het pictogram dat u wilt op een netwerk share. Ondersteunde afbeeldings indelingen zijn PNG, BMP, GIF, JPG, JPEG en ICO.

## <a name="publish-microsoft-edge"></a>Micro soft Edge publiceren

Het proces dat u gebruikt om micro soft Edge te publiceren, is iets anders dan het publicatie proces voor andere apps. Als u micro soft Edge wilt publiceren met de standaard startpagina, voert u deze cmdlet uit:

```powershell
New-AzWvdApplication -Name -ResourceGroupName -ApplicationGroupName -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" -CommandLineSetting <Allow|Require|DoNotAllow> -iconPath "C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedge.exe" -iconIndex 0 -ShowInPortal:$true
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het configureren van feeds om te organiseren hoe apps worden weer gegeven voor gebruikers bij het [aanpassen van de feed voor Windows-gebruikers met een virtueel bureau blad](customize-feed-for-virtual-desktop-users.md).
- Meer informatie over de functie voor het toevoegen van MSIX-apps bij het [instellen van MSIX app attach](app-attach.md).

