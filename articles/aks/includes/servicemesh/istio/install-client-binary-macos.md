---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 74f5b22ccc822a188059b29d9c661a15cf8412bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593914"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Baixe e instale o binário do cliente Istio istioctl

Em um shell baseado em bash `curl` no MacOS, use para `tar` baixar a versão istio e, em seguida, extrair com o seguinte:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

O `istioctl` binário do cliente é executado na sua máquina cliente e permite que você interaja com a malha de serviço istio. Use os seguintes comandos `istioctl` para instalar o binário do cliente Istio em um shell baseado em bash no MacOS. Esses comandos copiam o `istioctl` binário de cliente para o local do programa do usuário padrão no `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Se você quiser concluir a linha de comando do binário de cliente do Istio `istioctl`, configure-o conforme a seguir:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```