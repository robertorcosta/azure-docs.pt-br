---
title: Como atualizar as ferramentas do Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contêiner do Azure, contêineres
ms.openlocfilehash: 6ad28a5da05cf77cfaa834ed9752daefeb5f4fdb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279965"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Como atualizar as ferramentas do Azure Dev Spaces

Se houver uma nova versão e você já estiver usando o Azure Dev Spaces, talvez precise atualizar as ferramentas do seu cliente Azure Dev Spaces.

## <a name="update-the-azure-cli"></a>Atualizar a CLI do Azure

Quando você atualiza a CLI do Azure mais recente, também obtém a versão mais recente da extensão da CLI do Dev Spaces.

Não é necessário desinstalar a versão anterior, apenas localize o download apropriado na [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Atualizar a extensão da CLI e as ferramentas de linha de comando do Dev Spaces

Execute o seguinte comando:

```cmd
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Atualizar a extensão do VS Code

Após a instalação, a extensão é atualizada automaticamente. Talvez seja necessário recarregar a extensão para usar os novos recursos. No VS Code, abra o painel **Extensões**, escolha as extensões **Azure Dev Spaces** e escolha **Recarregar**.

## <a name="update-the-visual-studio-extension"></a>Atualizar a extensão do Visual Studio

Assim como ocorre com outras extensões e atualizações, o Visual Studio notificará você quando houver uma atualização disponível para o Ferramentas do Visual Studio para Kubernetes, o que inclui o Azure Dev Spaces. Procure um ícone de sinalizador na parte superior direita da tela.

Para atualizar as ferramentas no Visual Studio, escolha o item de menu **Ferramentas > Extensões e Atualizações** e, no lado esquerdo, escolha **Atualizações**. Encontre as **Ferramentas do Visual Studio para Kubernetes** e escolha o botão **Atualizar**.

## <a name="next-steps"></a>Próximas etapas

Teste as novas ferramentas criando um novo cluster. Experimente as guias de início rápido e tutoriais no [Azure Dev Spaces](/azure/dev-spaces).