---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "68639121"
---
De eenvoudigste manier om bindingextensies te installeren, is door [extensiebundels](../articles/azure-functions/functions-bindings-register.md#extension-bundles)in te schakelen. Wanneer u bundels inschakelt, wordt er automatisch een vooraf gedefinieerde set extensiepakketten geïnstalleerd.

Als u extensiebundels wilt inschakelen, opent u het bestand host.json en werkt u de inhoud daarvan zodanig bij dat deze overeenkomt met de volgende code:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```