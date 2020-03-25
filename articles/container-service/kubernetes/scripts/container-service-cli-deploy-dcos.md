---
title: Exemplo de script da CLI do Azure – criar o Cluster DC/SO do ACS
description: Exemplo de script da CLI do Azure – criar o Cluster DC/SO do ACS
author: iainfoulds
tags: acs, azure-container-service
keywords: Docker, Contêineres, Microsserviços, Kubernetes, DC/SO, Azure
ms.assetid: ''
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: b01d8d58da5a25ca9aa3d1ac16f10495fde8fc2b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76270535"
---
# <a name="deprecated-create-an-azure-container-service-dcos-cluster"></a>(PRETERIDO) Criar um cluster de DC/SO do Serviço de Contêiner do Azure

[!INCLUDE [ACS deprecation](../../../../includes/container-service-kubernetes-deprecation.md)]

Este exemplo cria um cluster do Serviço de Contêiner do Azure executando DC/SO.

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

```azurecli
az group create --name myResourceGroup --location eastus

az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az acs create](https://docs.microsoft.com/cli/azure/acs#az-acs-create) | Cria e cluster do ACS. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de script CLI do Serviço de Contêiner do Azure podem ser encontrados na [documentação do Serviço de Contêiner do Azure](../cli-samples.md).
