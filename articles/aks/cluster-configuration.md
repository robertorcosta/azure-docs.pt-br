---
title: Configuração de cluster nos serviços Kubernetess do Azure (AKS)
description: Saiba como configurar um cluster no serviço kubernetes do Azure (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479157"
---
# <a name="configure-an-aks-cluster"></a>Configurar um cluster do AKS

Como parte da criação de um cluster AKS, talvez seja necessário personalizar a configuração do cluster para atender às suas necessidades. Este artigo apresenta algumas opções para personalizar o cluster AKS.

## <a name="os-configuration-preview"></a>Configuração do so (versão prévia)

O AKS agora dá suporte ao Ubuntu 18, 4 como o sistema operacional do nó (SO) na versão prévia. Durante o período de visualização, ambos o Ubuntu 16, 4 e o Ubuntu 18, 4 estão disponíveis.

Você deve ter os seguintes recursos instalados:

- O CLI do Azure, versão 2.2.0 ou posterior
- A extensão 0.4.35 AKs-Preview

Para instalar a extensão AKs-preview 0.4.35 ou posterior, use os seguintes comandos de CLI do Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

Registre o `UseCustomizedUbuntuPreview` recurso:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o status seja exibido como **registrado**. Você pode verificar o status do registro usando o comando [AZ Feature List](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quando o status for exibido como registrado, atualize o registro do `Microsoft.ContainerService` provedor de recursos usando o comando [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Configure o cluster para usar o Ubuntu 18, 4 quando o cluster for criado. Use o `--aks-custom-headers` sinalizador para definir o Ubuntu 18, 4 como o sistema operacional padrão.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se você quiser criar um cluster do Ubuntu 16, 4 regular, poderá fazer isso omitindo a marca personalizada `--aks-custom-headers` .

## <a name="custom-resource-group-name"></a>Nome do grupo de recursos personalizado

Quando você implanta um cluster do serviço kubernetes do Azure no Azure, um segundo grupo de recursos é criado para os nós de trabalho. Por padrão, AKS nomeará o grupo `MC_resourcegroupname_clustername_location`de recursos do nó, mas você também poderá fornecer seu próprio nome.

Para especificar seu próprio nome de grupo de recursos, instale o AKs-Preview CLI do Azure versão de extensão 0.3.2 ou posterior. Usando o CLI do Azure, use o `--node-resource-group` parâmetro do `az aks create` comando para especificar um nome personalizado para o grupo de recursos. Se você usar um modelo de Azure Resource Manager para implantar um cluster AKS, poderá definir o nome do grupo de recursos usando `nodeResourceGroup` a propriedade.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

O grupo de recursos secundário é criado automaticamente pelo provedor de recursos do Azure em sua própria assinatura. Observe que você só pode especificar o nome do grupo de recursos personalizado quando o cluster é criado. 

Ao trabalhar com o grupo de recursos do nó, tenha em mente que não é possível:

- Especifique um grupo de recursos existente para o grupo de recursos do nó.
- Especifique uma assinatura diferente para o grupo de recursos do nó.
- Altere o nome do grupo de recursos do nó depois que o cluster tiver sido criado.
- Especifique nomes para os recursos gerenciados dentro do grupo de recursos do nó.
- Modifique ou exclua as marcas criadas pelo Azure de recursos gerenciados dentro do grupo de recursos do nó.

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar `Kured` o para [aplicar atualizações de segurança e kernel a nós do Linux](node-updates-kured.md) em seu cluster.
- Consulte [atualizar um cluster do AKS (serviço kubernetes do Azure)](upgrade-cluster.md) para saber como atualizar o cluster para a versão mais recente do kubernetes.
- Consulte a lista de [perguntas](faq.md) frequentes sobre o AKs para encontrar respostas para algumas perguntas comuns sobre o AKs.