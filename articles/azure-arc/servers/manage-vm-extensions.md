---
title: VM-extensie beheer met servers die geschikt zijn voor Azure-Arc
description: Servers met Azure-Arc kunnen de implementatie van virtuele-machine uitbreidingen beheren die configuratie van de na de implementatie en Automation-taken bieden met niet-Azure Vm's.
ms.date: 11/06/2020
ms.topic: conceptual
ms.openlocfilehash: 7682f6c8631bbaf2310d501d7cee6aecb2311226
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358028"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Extensie beheer voor virtuele machines met servers die geschikt zijn voor Azure-Arc

Virtuele-machine-uitbrei dingen zijn kleine toepassingen die configuratie-en automatiserings taken na de implementatie leveren op Azure-Vm's. Als op een virtuele machine bijvoorbeeld software of antivirusbeveiliging moet worden geïnstalleerd of een script moet worden uitgevoerd, kan hiervoor een VM-extensie worden gebruikt.

Met Azure Arc-servers kunt u Azure-VM-extensies implementeren op niet-Azure Windows-en Linux-Vm's, waardoor het beheer van uw hybride machine on-premises, Edge en andere Cloud omgevingen via hun levens cyclus wordt vereenvoudigd. VM-extensies kunnen worden beheerd met behulp van de volgende methoden op uw hybride computers of servers die worden beheerd door servers met Arc-functionaliteit:

- De [Azure Portal](manage-vm-extensions-portal.md)
- De [Azure cli](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager-sjablonen](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Belangrijkste voordelen

Ondersteuning voor VM-extensies voor Azure Arc ingeschakelde servers biedt de volgende belang rijke voor delen:

- Gebruik [Azure Automation status configuratie](../../automation/automation-dsc-overview.md) om configuraties centraal op te slaan en de gewenste status van hybride verbonden computers te onderhouden die zijn ingeschakeld via de DSC-extensie voor vm's.

- Verzamel logboek gegevens voor analyse met [Logboeken in azure monitor](../../azure-monitor/platform/data-platform-logs.md) ingeschakeld via de VM-extensie van de log Analytics agent. Dit is handig voor het uitvoeren van complexe analyses op basis van gegevens uit verschillende bronnen.

- Met [Azure monitor voor VM's](../../azure-monitor/insights/vminsights-overview.md)kunt u de prestaties van uw Windows-en Linux-vm's analyseren en de processen en afhankelijkheden van andere bronnen en externe processen controleren. Dit wordt bereikt door zowel de Log Analytics agent als de VM-extensies voor de afhankelijkheids agent in te scha kelen.

- Down load en voer scripts uit op hybride verbonden computers met de extensie voor aangepaste scripts. Deze uitbrei ding is handig voor configuratie na de implementatie, software-installatie of andere configuratie-of beheer taken.

- Certificaten die zijn opgeslagen in een [Azure Key Vault](../../key-vault/general/overview.md)automatisch vernieuwen.

## <a name="availability"></a>Beschikbaarheid

De functionaliteit van de VM-extensie is alleen beschikbaar in de lijst met [ondersteunde regio's](overview.md#supported-regions). Zorg ervoor dat u uw computer in een van deze regio's onboardt.

## <a name="extensions"></a>Extensies

In deze release ondersteunen we de volgende VM-extensies op Windows-en Linux-computers.

|Extensie |Besturingssysteem |Publisher |Aanvullende informatie |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Aangepaste script extensie voor Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Micro soft. Power shell|[Windows Power shell DSC-extensie](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics-agent |Windows |Micro soft. EnterpriseCloud. monitoring |[VM-extensie Log Analytics voor Windows](../../virtual-machines/extensions/oms-windows.md)|
|Micro soft-afhankelijkheids agent | Windows |Microsoft.Compute | [Extensie van de virtuele machine van de afhankelijkheids agent voor Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Key Vault | Windows | Microsoft.Compute | [Extensie van de virtuele machine Key Vault voor Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|CustomScript|Linux |Micro soft. Azure. extension |[Aangepaste script extensie voor Linux versie 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Micro soft. OSTCExtensions |[Power shell DSC-extensie voor Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics-agent |Linux |Micro soft. EnterpriseCloud. monitoring |[VM-extensie Log Analytics voor Linux](../../virtual-machines/extensions/oms-linux.md) |
|Micro soft-afhankelijkheids agent | Linux |Microsoft.Compute | [Extensie van de virtuele machine van de afhankelijkheids agent voor Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Key Vault | Linux | Microsoft.Compute | [Extensie van de virtuele machine Key Vault voor Linux](../../virtual-machines/extensions/key-vault-linux.md) |

Zie [overzicht van agents](agent-overview.md#agent-component-details)voor meer informatie over het Azure Connected machine agent-pakket en Details over het onderdeel van de extensie agent.

## <a name="prerequisites"></a>Vereisten

Deze functie is afhankelijk van de volgende Azure-resource providers in uw abonnement:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Als ze nog niet zijn geregistreerd, volgt u de stappen onder [Azure-resource providers registreren](agent-overview.md#register-azure-resource-providers).

### <a name="log-analytics-vm-extension"></a>VM-extensie Log Analytics

Voor de VM-extensie van de Log Analytics-agent voor Linux is Python 2. x geïnstalleerd op de doel computer.

### <a name="azure-key-vault-vm-extension-preview"></a>VM-extensie Azure Key Vault (preview-versie)

De Key Vault VM-extensie (preview) biedt geen ondersteuning voor de volgende Linux-besturings systemen:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

De implementatie van de Key Vault VM-extensie (preview) wordt alleen ondersteund met behulp van:

- De Azure CLI
- De Azure PowerShell
- Azure Resource Manager-sjabloon

Voordat u de uitbrei ding implementeert, moet u het volgende volt ooien:

1. [Een kluis en een certificaat maken](../../key-vault/certificates/quick-create-portal.md) (zelf ondertekend of geïmporteerd).

2. Verleen de Azure Arc-server toegang tot het certificaat geheim. Als u gebruikmaakt van de [RBAC-preview](../../key-vault/general/rbac-guide.md), zoekt u naar de naam van de Azure-Arc-resource en wijst u deze toe aan de rol **Key Vault geheimen gebruiker (preview)** . Als u [Key Vault toegangs beleid](../../key-vault/general/assign-access-policy-portal.md)gebruikt, wijst u geheim **Get** -machtigingen toe aan de door het systeem toegewezen identiteit van de Azure-Arc-resource.

### <a name="connected-machine-agent"></a>Verbonden machine agent

Controleer of uw computer overeenkomt met de [ondersteunde versies](agent-overview.md#supported-operating-systems) van het Windows-en Linux-besturings systeem voor de Azure Connected machine-agent.

De minimale versie van de verbonden machine agent die wordt ondersteund door deze functie in Windows en Linux is de 1,0-release.

Zie [upgrade agent](manage-agent.md#upgrading-agent)om uw computer te upgraden naar de versie van de vereiste agent.

## <a name="next-steps"></a>Volgende stappen

U kunt VM-extensies implementeren, beheren en verwijderen met behulp van de [Azure cli](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md), vanuit de [Azure Portal](manage-vm-extensions-portal.md)of [Azure Resource Manager sjablonen](manage-vm-extensions-template.md).
