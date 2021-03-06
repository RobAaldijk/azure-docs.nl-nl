---
title: Overzicht van de sms-clientbibliotheek voor Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Hierin wordt een overzicht gegeven van de sms-clientbibliotheek en de bijbehorende aanbiedingen.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d40637b05a5d398124ee52dbca49d55b3112ec95
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758401"
---
# <a name="sms-client-library-overview"></a>Overzicht van de sms-clientbibliotheek

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Sms-clientbibliotheken van Azure Communication Services kunnen worden gebruikt om functionaliteit voor sms-berichten aan uw toepassingen toe te voegen.

## <a name="sms-client-library-capabilities"></a>Mogelijkheden van sms-clientbibliotheken

De volgende lijst bevat de set functies die momenteel beschikbaar zijn in onze clientbibliotheken.

| Groep van functies | Mogelijkheid                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Belangrijkste mogelijkheden | Sms-berichten verzenden en ontvangen </br> *Ondersteuning voor Unicode-emoji's*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Bezorgingsrapporten ontvangen voor verzonden berichten                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Alle tekensets (taal-/Unicode-ondersteuning)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Ondersteuning voor lange berichten (maximaal 2048 tekens)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Automatische samenvoeging van lange berichten                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| Gebeurtenissen            | Event Grid gebruiken om webhooks te configureren voor het ontvangen van binnenkomende berichten en bezorgingsrapporten | ✔️   | ✔️    | ✔️    | ✔️      |
| Telefoonnummer      | Gratis nummers                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Regelgeving        | Afhandeling van uitschrijving                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Bewaking        | Gebruik bewaken voor verzonden en ontvangen berichten                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| Bellen via PSTN      | Mogelijkheden voor bellen via PSTN toevoegen aan uw gratis nummer met sms-functionaliteit                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met het verzenden van sms-berichten](../../quickstarts/telephony-sms/send.md)

De volgende documenten zijn mogelijk interessant voor u:

- Zorg dat u vertrouwd raakt met algemene [sms-concepten](../telephony-sms/concepts.md)
- Koop een [telefoonnummer](../../quickstarts/telephony-sms/get-phone-number.md) dat geschikt is voor sms
- [Plan uw sms-oplossing](../telephony-sms/plan-solution.md)
