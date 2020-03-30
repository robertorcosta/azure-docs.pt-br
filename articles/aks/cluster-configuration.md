---
title: Configuração de cluster no Azure Kubernetes Services (AKS)
description: Saiba como configurar um cluster no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479157"
---
# <a name="configure-an-aks-cluster"></a>Configurar um cluster do AKS

Como parte da criação de um cluster AKS, talvez seja necessário personalizar sua configuração de cluster para atender às suas necessidades. Este artigo introduz algumas opções para personalizar seu cluster AKS.

## <a name="os-configuration-preview"></a>Configuração do Sistema Operacional (Visualização)

O AKS agora suporta o Ubuntu 18.04 como o sistema operacional de nó (OS) na pré-visualização. Durante o período de pré-visualização, tanto o Ubuntu 16.04 quanto o Ubuntu 18.04 estão disponíveis.

Você deve ter os seguintes recursos instalados:

- O Azure CLI, versão 2.2.0 ou posterior
- A extensão aks-preview 0.4.35

Para instalar a extensão aks-preview 0.4.35 ou posterior, use os seguintes comandos Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

Registre `UseCustomizedUbuntuPreview` o recurso:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para o status aparecer como **Registrado**. Você pode verificar o status do registro usando o comando [az feature list:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quando o status aparecer como registrado, `Microsoft.ContainerService` atualize o registro do provedor de recursos usando o comando [de registro do provedor az:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Configure o cluster para usar o Ubuntu 18.04 quando o cluster for criado. Use `--aks-custom-headers` o sinalizador para definir o Ubuntu 18.04 como o sistema operacional padrão.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se você quiser criar um cluster Ubuntu 16.04 regular, você `--aks-custom-headers` pode fazê-lo omitindo a tag personalizada.

## <a name="custom-resource-group-name"></a>Nome do grupo de recursos personalizado

Quando você implanta um cluster de serviço saqueado no Azure, um segundo grupo de recursos é criado para os nós do trabalhador. Por padrão, a AKS nomeará `MC_resourcegroupname_clustername_location`o grupo de recursos do nó, mas você também pode fornecer seu próprio nome.

Para especificar o nome do seu próprio grupo de recursos, instale a versão aks-preview Azure CLI versão 0.3.2 ou posterior. Usando o Cli do Azure, use o `--node-resource-group` parâmetro do `az aks create` comando para especificar um nome personalizado para o grupo de recursos. Se você usar um modelo do Azure Resource Manager para implantar um cluster `nodeResourceGroup` AKS, você poderá definir o nome do grupo de recursos usando a propriedade.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

O grupo de recursos secundários é criado automaticamente pelo provedor de recursos Do Azure em sua própria assinatura. Observe que você só pode especificar o nome do grupo de recursos personalizado quando o cluster for criado. 

Ao trabalhar com o grupo de recursos do nó, tenha em mente que não pode:

- Especifique um grupo de recursos existente para o grupo de recursos do nó.
- Especifique uma assinatura diferente para o grupo de recursos do nó.
- Alterar o nome do grupo de recursos do nó após a criação do cluster.
- Especifique nomes para os recursos gerenciados no grupo de recursos do nó.
- Modificar ou excluir as tags criadas pelo Azure de recursos gerenciados dentro do grupo de recursos do nó.

## <a name="next-steps"></a>Próximas etapas

- Aprenda a `Kured` usar para [aplicar atualizações de segurança e kernel aos nós Linux](node-updates-kured.md) em seu cluster.
- Consulte [Atualizar um cluster Azure Kubernetes Service (AKS)](upgrade-cluster.md) para saber como atualizar seu cluster para a versão mais recente do Kubernetes.
- Veja a lista de [perguntas frequentes sobre AKS](faq.md) para encontrar respostas para algumas perguntas comuns da AKS.