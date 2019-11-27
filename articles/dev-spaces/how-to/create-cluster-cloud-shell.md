---
title: Criar um cluster kubernetes com Azure Dev Spaces habilitado-Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Aprenda a criar rapidamente um cluster do Kubernetes habilitado para Azure Dev Spaces diretamente do navegador sem instalar nada.
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: ac825caa7aacbc6ac21dd1f9fe7acbb9cf12e900
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325766"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Criar um cluster kubernetes com o Azure Dev Spaces habilitado com Azure Cloud Shell

Você pode usar [Azure cloud Shell](/azure/cloud-shell) para criar um cluster do serviço kubernetes do Azure usando o botão **experimentar** nesta página. Se não estiver conectado, siga os prompts para entrar com uma conta do Azure e, em seguida, digite os comandos no prompt do Azure Cloud Shell quando for exibido.

## <a name="create-the-cluster"></a>Criar o cluster

Primeiro, crie o grupo de recursos em uma [região com suporte a Azure dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Crie um cluster Kubernetes com o seguinte comando:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

São necessários alguns minutos para criar o cluster.  Quando concluída, a saída será mostrada no formato JSON. Procure `provisioningState` e verifique se é `Succeeded`.

## <a name="next-steps"></a>Próximas etapas

Consulte [Azure Dev Spaces](/azure/dev-spaces/) para obter links e ter acesso a tutoriais completos.

> [!IMPORTANT]
> Muitos dos guias de início rápido e tutoriais do Azure Dev Spaces usam a CLI do Azure Dev Spaces para executar operações. Não é possível instalar a CLI do Azure Dev Spaces no Azure Cloud Shell.


[supported-regions]: ../about.md#supported-regions-and-configurations