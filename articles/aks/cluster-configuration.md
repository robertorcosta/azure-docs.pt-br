---
title: Configuração de cluster no AKS (Serviços de Kubernetes do Azure)
description: Saiba como configurar um cluster no AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fe5ce13d9db8f2bc2231f87de7e602e63d239bfa
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725139"
---
# <a name="configure-an-aks-cluster"></a>Configurar um cluster do AKS

Como parte da criação de um cluster do AKS, talvez seja necessário personalizar a configuração do cluster para atender às suas necessidades. Este artigo apresenta algumas opções para personalizar o cluster do AKS.

## <a name="os-configuration-preview"></a>Configuração do SO (versão prévia)

O AKS agora dá suporte ao Ubuntu 18.04 como o SO (sistema operacional) do nó em versão prévia. Durante o período de versão prévia, tanto o Ubuntu 16.04 quanto o Ubuntu 18.04 está disponível.

Você deve ter os seguintes recursos instalados:

- A CLI do Azure versão 2.2.0 ou posterior
- A extensão da versão prévia do AKS 0.4.35

Para instalar a extensão da versão prévia do AKS 0.4.35 ou posterior, use os seguintes comandos da CLI do Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

Registrar o recurso `UseCustomizedUbuntuPreview`:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o status seja exibido como **Registrado**. Você pode verificar o status de registro usando o comando [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quando o status aparecer como registrado, atualize o registro do provedor de recursos `Microsoft.ContainerService` usando o comando [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register):

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="new-clusters"></a>Novos clusters

Configure o cluster para usar o Ubuntu 18.04 quando o cluster for criado. Use o sinalizador `--aks-custom-headers` para definir o Ubuntu 18.04 como o SO padrão.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se você quiser criar um cluster do Ubuntu 16.04 regular, poderá fazer isso omitindo a tag `--aks-custom-headers` personalizada.

### <a name="existing-clusters"></a>Clusters existentes

Configure um novo pool de nós para usar o Ubuntu 18.04. Use o sinalizador `--aks-custom-headers` para definir o Ubuntu 18.04 como o SO padrão para esse pool de nós.

```azure-cli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se você quiser criar um pool de nós do Ubuntu 16.04 regular, poderá fazer isso omitindo a tag `--aks-custom-headers` personalizada.


## <a name="custom-resource-group-name"></a>Nome do grupo de recursos personalizado

Quando você implanta um cluster do Serviço de Kubernetes do Azure no Azure, um segundo grupo de recursos é criado para os nós de trabalho. Por padrão, o AKS dará ao grupo de recursos do nó o nome `MC_resourcegroupname_clustername_location`, mas você também poderá fornecer seu próprio nome.

Para especificar um nome do grupo de recursos, instale a extensão da CLI do Azure da versão prévia do AKS versão 0.3.2 ou posterior. Usando a CLI do Azure, use o parâmetro `--node-resource-group` do comando `az aks create` para especificar um nome personalizado para o grupo de recursos. Se você usar um modelo do Azure Resource Manager para implantar um cluster do AKS, defina o nome do grupo de recursos usando a propriedade `nodeResourceGroup`.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

O grupo de recursos secundário é criado automaticamente pelo provedor de recursos do Azure na própria assinatura. Você só pode especificar o nome do grupo de recursos personalizado quando o cluster é criado. 

Ao trabalhar com o grupo de recursos do nó, tenha em mente que não é possível:

- Especificar um grupo de recursos existente para o grupo de recursos do nó.
- Especificar uma assinatura diferente para o grupo de recursos do nó.
- Alterar o nome do grupo de recursos do nó depois que o cluster for criado.
- Especificar nomes para os recursos gerenciados dentro do grupo de recursos do nó.
- Modificar ou excluir marcas criadas pelo Azure de recursos gerenciados dentro do grupo de recursos do nó.

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar `Kured` para [aplicar atualizações de segurança e kernel a nós do Linux](node-updates-kured.md) no cluster.
- Confira [Atualizar um cluster do AKS (Serviço de Kubernetes do Azure)](upgrade-cluster.md) para saber como atualizar o cluster para a versão mais recente do Kubernetes.
- Confira a lista de [Perguntas frequentes sobre o AKS](faq.md) para encontrar respostas a algumas perguntas comuns sobre o AKS.