---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593694"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Baixe e instale o linkerd linkerd client binário

Em um shell baseado em bash `curl` no MacOS, use para baixar a versão linkerd da seguinte forma:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

O `linkerd` binário do cliente é executado na sua máquina cliente e permite que você interaja com a malha de serviço linkerd. Use os seguintes comandos para `linkerd` instalar o binário do cliente Linkerd em um shell baseado em bash no MacOS. Esses comandos copiam o `linkerd` binário de cliente para o local do programa do usuário padrão no `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Se você quiser concluir a linha de `linkerd` comando para o binário do cliente Linkerd, configure-o da seguinte forma:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
