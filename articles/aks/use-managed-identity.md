---
title: Usar identidades gerenciadas no serviço kubernetes do Azure
description: Saiba como usar identidades gerenciadas no serviço kubernetes do Azure (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 00ecc077ba55ab9f91fc58f8a47fcdf7440deea6
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82112959"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Usar identidades gerenciadas no serviço kubernetes do Azure

Atualmente, um cluster AKS (serviço de kubernetes do Azure) (especificamente, o provedor de nuvem kubernetes) requer uma identidade para criar recursos adicionais, como balanceadores de carga e discos gerenciados no Azure, essa identidade pode ser uma *identidade gerenciada* ou uma *entidade de serviço*. Se você usar uma [entidade de serviço](kubernetes-service-principal.md), deverá fornecer uma ou AKs cria uma em seu nome. Se você usar a identidade gerenciada, ela será criada para você pelo AKS automaticamente. Os clusters que usam entidades de serviço eventualmente atingem um estado no qual a entidade de serviço deve ser renovada para manter o cluster funcionando. O gerenciamento de entidades de serviço adiciona complexidade, motivo pelo qual é mais fácil usar identidades gerenciadas. Os mesmos requisitos de permissão se aplicam a entidades de serviço e identidades gerenciadas.

*Identidades gerenciadas* são essencialmente um wrapper em relação às entidades de serviço e tornam seu gerenciamento mais simples. Para saber mais, leia sobre [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

O AKS cria duas identidades gerenciadas:

- **Identidade gerenciada atribuída pelo sistema**: a identidade que o provedor de nuvem kubernetes usa para criar recursos do Azure em nome do usuário. O ciclo de vida da identidade atribuída pelo sistema está vinculado ao do cluster. A identidade é excluída quando o cluster é excluído.
- **Identidade gerenciada atribuída pelo usuário**: a identidade que é usada para autorização no cluster. Por exemplo, a identidade atribuída pelo usuário é usada para autorizar o AKS a usar registros de contêiner do Azure (ACRs) ou autorizar o kubelet a obter metadados do Azure.

Os complementos também são autenticados usando uma identidade gerenciada. Para cada complemento, uma identidade gerenciada é criada por AKS e dura a vida útil do complemento. 

## <a name="before-you-begin"></a>Antes de começar

Você deve ter o seguinte recurso instalado:

- O CLI do Azure, versão 2.2.0 ou posterior

## <a name="create-an-aks-cluster-with-managed-identities"></a>Criar um cluster AKS com identidades gerenciadas

Agora você pode criar um cluster AKS com identidades gerenciadas usando os comandos da CLI a seguir.

Primeiro, crie um grupo de recursos do Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Em seguida, crie um cluster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Uma criação de cluster bem-sucedida usando identidades gerenciadas contém estas informações de perfil de entidade de serviço:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

> [!NOTE]
> Para criar e usar sua própria VNet, endereço IP estático ou disco do Azure anexado em que os recursos estão fora do MC_ * grupo de recursos, use a entidade de segurança do sistema de cluster atribuída à identidade gerenciada para executar uma atribuição de função. Para obter mais informações sobre a atribuição de função, consulte [delegar acesso a outros recursos do Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> As concessões de permissão para a identidade gerenciada do cluster usada pelo provedor de nuvem do Azure podem levar de 60 minutos para popular.

Por fim, obtenha as credenciais para acessar o cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

O cluster será criado em alguns minutos. Em seguida, você pode implantar suas cargas de trabalho de aplicativo no novo cluster e interagir com ela da mesma forma como você fez com clusters AKS com base na entidade de serviço.

> [!IMPORTANT]
>
> - Os clusters AKS com identidades gerenciadas podem ser habilitados somente durante a criação do cluster.
> - Os clusters AKS existentes não podem ser atualizados ou atualizados para habilitar identidades gerenciadas.
