---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: d303def85d66d5c2039b426e48186f90c2b3fff6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86277764"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Down load en installeer het binaire bestand voor de Linkerd Linkerd-client

In een bash shell op Linux of Windows- [subsysteem voor Linux][install-wsl]kunt u `curl` de Linkerd-release als volgt downloaden:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

De `linkerd` binaire client wordt uitgevoerd op uw client computer en maakt het mogelijk om te communiceren met het Linkerd-service-net. Gebruik de volgende opdrachten om de Linkerd- `linkerd` client binaire bestanden te installeren in een op bash gebaseerde shell op Linux of [Windows-subsysteem voor Linux][install-wsl]. Met deze opdrachten `linkerd` wordt de binaire client gekopieerd naar de standaard gebruikers programma locatie in uw `PATH` .

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Als u de opdracht regel voor het binaire bestand van de Linkerd-client wilt laten volt ooien, moet u `linkerd` deze als volgt instellen:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: /windows/wsl/install-win10
