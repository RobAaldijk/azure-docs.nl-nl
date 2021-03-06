---
title: Include-bestand
description: Include-bestand
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 354015930170ca6466b3555c78d211c080232c82
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755928"
---
### <a name="to-view-local-network-gateways"></a>Lokale netwerkgateways bekijken

Gebruik de opdracht [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway) om een lijst met gateways voor lokale netwerken weer te geven.

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>De gedeelde sleutelwaarden controleren

Controleer of de waarde van de gedeelde sleutel gelijk is aan de waarde die u voor de configuratie van uw VPN-apparaat hebt gebruikt. Als dat niet het geval is, voert u de verbinding opnieuw uit met de waarde van het apparaat of werkt u het apparaat bij met de waarde die is geretourneerd. De waarden moeten overeenkomen. Gebruik [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection) om de gedeelde sleutel weer te geven.

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>Het openbare IP-adres van de VPN Gateway weergeven

  Gebruik de opdracht [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip) om het openbare IP-adres van uw virtuele netwerkgateway te vinden. De lijst met openbare IP-adressen wordt in dit voorbeeld in een gemakkelijk leesbare tabelindeling weergegeven.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
