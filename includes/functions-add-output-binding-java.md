---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673501"
---
In een Java-project worden de bindingen gedefinieerd als bindingsaantekeningen in de functiemethode. Het bestand *function.json* wordt vervolgens automatisch gegenereerd op basis van deze aantekeningen.

Blader naar de locatie van de functiecode onder _src/main/java_, open het projectbestand *Function.java* en voeg de volgende parameter aan de methodedefinitie `run` toe:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

Parameter `msg` is van het type [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), dat een verzameling berichten voorstelt die naar een uitvoerbinding wordt geschreven wanneer de functie is voltooid. In dit geval is de uitvoer een opslagwachtrij met de naam `outqueue`. De verbindingsreeks voor het Storage-account wordt ingesteld door de methode `connection`. In plaats van de verbindingsreeks zelf, geeft u de toepassingsinstelling door die de verbindingsreeks voor het Storage-account bevat.

De methodedefinitie `run` moet er nu uitzien als in het volgende voorbeeld:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::