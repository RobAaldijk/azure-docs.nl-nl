---
title: Een persoonlijk eind punt configureren
titleSuffix: Azure Machine Learning
description: Persoonlijke Azure-koppeling gebruiken om veilig toegang te krijgen tot uw Azure Machine Learning-werk ruimte vanuit een virtueel netwerk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: cfa6dcb0b1fc173bdf3612308227b8309491e9fa
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312732"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Een persoonlijke Azure-koppeling configureren voor een Azure Machine Learning-werk ruimte

In dit document leert u hoe u een persoonlijke Azure-koppeling kunt gebruiken met uw Azure Machine Learning-werk ruimte. Zie [Virtual Network-isolatie en privacy-Overzicht](how-to-network-security-overview.md) voor meer informatie over het maken van een virtueel netwerk voor Azure machine learning.

Met de persoonlijke Azure-koppeling kunt u verbinding maken met uw werk ruimte met behulp van een persoonlijk eind punt. Het persoonlijke eind punt is een reeks privé-IP-adressen in uw virtuele netwerk. Vervolgens kunt u de toegang tot uw werk ruimte beperken tot alleen de privé-IP-adressen. Een persoonlijke koppeling helpt het risico van gegevens exfiltration te verminderen. Zie het artikel over een [persoonlijke Azure-koppeling](../private-link/private-link-overview.md) voor meer informatie over privé-eind punten.

> [!IMPORTANT]
> Persoonlijke Azure-koppeling heeft geen invloed op Azure Control-vlak (beheer bewerkingen), zoals het verwijderen van de werk ruimte of het beheren van reken resources. Bijvoorbeeld maken, bijwerken of verwijderen van een berekenings doel. Deze bewerkingen worden normaal gesp roken uitgevoerd via het open bare Internet. Gegevenslaag bewerkingen, zoals het gebruik van Azure Machine Learning Studio, Api's (inclusief gepubliceerde pijp lijnen) of de SDK gebruiken het persoonlijke eind punt.
>
> Er kunnen problemen optreden bij het openen van het persoonlijke eind punt voor uw werk ruimte als u gebruikmaakt van Mozilla Firefox. Dit probleem kan betrekking hebben op DNS via HTTPS in Mozilla. We raden u aan micro soft Edge van Google Chrome als tijdelijke oplossing te gebruiken.

## <a name="prerequisites"></a>Vereisten

Als u van plan bent een persoonlijke koppeling in te scha kelen met een door de klant beheerde sleutel, moet u deze functie aanvragen met behulp van een ondersteunings ticket. Zie [Quota's beheren en verhogen](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)voor meer informatie.

## <a name="limitations"></a>Beperkingen

Het gebruik van een Azure Machine Learning werk ruimte met een persoonlijke koppeling is niet beschikbaar in de regio's Azure Government regio's en Azure China 21Vianet.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Een werk ruimte maken die gebruikmaakt van een persoonlijk eind punt

Gebruik een van de volgende methoden om een werk ruimte met een persoonlijk eind punt te maken. Voor elk van deze methoden __is een bestaand virtueel netwerk vereist__ :

> [!TIP]
> Als u op hetzelfde moment een werk ruimte, een persoonlijk eind punt en een virtueel netwerk wilt maken, raadpleegt u een [Azure Resource Manager sjabloon gebruiken om een werk ruimte voor Azure machine learning te maken](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

De Azure Machine Learning python SDK biedt de klasse [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) , die kan worden gebruikt met de [werk ruimte. Create ()](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) om een werk ruimte met een persoonlijk eind punt te maken. Voor deze klasse is een bestaand virtueel netwerk vereist.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De [Azure cli-extensie voor machine learning](reference-azure-machine-learning-cli.md) biedt de opdracht [AZ ml Workspace Create](/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) . De volgende para meters voor deze opdracht kunnen worden gebruikt om een werk ruimte met een particulier netwerk te maken, maar hiervoor is een bestaand virtueel netwerk vereist:

* `--pe-name`: De naam van het persoonlijke eind punt dat is gemaakt.
* `--pe-auto-approval`: Of privé-eindpunt verbindingen met de werk ruimte automatisch moeten worden goedgekeurd.
* `--pe-resource-group`: De resource groep waarin het persoonlijke eind punt moet worden gemaakt. Moet dezelfde groep zijn die het virtuele netwerk bevat.
* `--pe-vnet-name`: Het bestaande virtuele netwerk voor het maken van het persoonlijke eind punt in.
* `--pe-subnet-name`: De naam van het subnet waarin het persoonlijke eind punt moet worden gemaakt. De standaardwaarde is `default`.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Op het tabblad __netwerken__ in azure machine learning Studio kunt u een persoonlijk eind punt configureren. Er is echter wel een bestaand virtueel netwerk nodig. Zie [werk ruimten maken in de portal](how-to-manage-workspace.md)voor meer informatie.

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Een persoonlijk eind punt toevoegen aan een werk ruimte

Gebruik een van de volgende methoden om een persoonlijk eind punt toe te voegen aan een bestaande werk ruimte:

> [!IMPORTANT]
>
> U moet een bestaand virtueel netwerk hebben om het persoonlijke eind punt in te kunnen maken. U moet ook [netwerk beleid voor persoonlijke eind punten uitschakelen](../private-link/disable-private-endpoint-network-policy.md) voordat u het persoonlijke eind punt toevoegt.

> [!WARNING]
>
> Als u bestaande reken doelen hebt die aan deze werk ruimte zijn gekoppeld, en ze zich niet achter hetzelfde virtuele netwerk bevinden tha en het persoonlijke eind punt wordt gemaakt in, werken ze niet.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Zie [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) en [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-)voor meer informatie over de klassen en methoden die in dit voor beeld worden gebruikt.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De [Azure cli-extensie voor machine learning](reference-azure-machine-learning-cli.md) biedt de opdracht [AZ ml Workspace private-endpoint add](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_add) .

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer in de werk ruimte Azure Machine Learning in de portal __persoonlijke eindpunt verbindingen__ en selecteer vervolgens __+ persoonlijk eind punt__. Gebruik de velden om een nieuw persoonlijk eind punt te maken.

* Wanneer u de __regio__ selecteert, selecteert u dezelfde regio als het virtuele netwerk. 
* Gebruik __micro soft. MachineLearningServices/werk ruimten__ bij het selecteren van het __resource type__. 
* Stel de __resource__ in op de naam van uw werk ruimte.

Selecteer ten slotte __maken__ om het persoonlijke eind punt te maken.

---

## <a name="remove-a-private-endpoint"></a>Een persoonlijk eind punt verwijderen

Gebruik een van de volgende methoden om een persoonlijk eind punt uit een werk ruimte te verwijderen:

# <a name="python"></a>[Python](#tab/python)

Gebruik [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#delete-private-endpoint-connection-private-endpoint-connection-name-) om een persoonlijk eind punt te verwijderen.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De [Azure cli-extensie voor machine learning](reference-azure-machine-learning-cli.md) biedt de opdracht [AZ ml Workspace private-endpoint delete](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_delete) .

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer in de werk ruimte Azure Machine Learning in de portal __persoonlijke eindpunt verbindingen__ en selecteer vervolgens het eind punt dat u wilt verwijderen. Selecteer ten slotte __verwijderen__.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Een werk ruimte gebruiken via een persoonlijk eind punt

Omdat de communicatie met de werk ruimte alleen is toegestaan vanuit het virtuele netwerk, moeten ontwikkel omgevingen die gebruikmaken van de werk ruimte lid zijn van het virtuele netwerk. Bijvoorbeeld een virtuele machine in het virtuele netwerk.

> [!IMPORTANT]
> Om te voor komen dat de verbinding tijdelijk wordt verbroken, raadt micro soft aan de DNS-cache te wissen op computers die verbinding maken met de werk ruimte nadat een persoonlijke koppeling is ingeschakeld. 

Raadpleeg de [virtual machines documentatie](../virtual-machines/index.yml)voor meer informatie over Azure virtual machines.


## <a name="next-steps"></a>Volgende stappen

* Zie het artikel [overzicht van virtuele netwerken en privacy](how-to-network-security-overview.md) voor meer informatie over het beveiligen van uw Azure machine learning-werk ruimte.

* Als u van plan bent een aangepaste DNS-oplossing te gebruiken in uw virtuele netwerk, raadpleegt u [een werk ruimte gebruiken met een aangepaste DNS-server](how-to-custom-dns.md).