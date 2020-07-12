---
title: Usar identidades gerenciadas no serviço kubernetes do Azure
description: Saiba como usar identidades gerenciadas no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.topic: article
ms.date: 07/10/2020
ms.author: mlearned
ms.openlocfilehash: 95a303a4b6a83901560b26679bca920b9de4d3f4
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86250898"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Usar identidades gerenciadas no serviço kubernetes do Azure

Atualmente, um cluster AKS (serviço de kubernetes do Azure) (especificamente, o provedor de nuvem kubernetes) requer uma identidade para criar recursos adicionais, como balanceadores de carga e discos gerenciados no Azure. Essa identidade pode ser uma *identidade gerenciada* ou uma *entidade de serviço*. Se você usar uma [entidade de serviço](kubernetes-service-principal.md), deverá fornecer uma ou AKs cria uma em seu nome. Se você usar a identidade gerenciada, ela será criada para você pelo AKS automaticamente. Os clusters que usam entidades de serviço eventualmente atingem um estado no qual a entidade de serviço deve ser renovada para manter o cluster funcionando. O gerenciamento de entidades de serviço adiciona complexidade, motivo pelo qual é mais fácil usar identidades gerenciadas. Os mesmos requisitos de permissão se aplicam a entidades de serviço e identidades gerenciadas.

*Identidades gerenciadas* são essencialmente um wrapper em relação às entidades de serviço e tornam seu gerenciamento mais simples. A rotação de credenciais para MI ocorre automaticamente a cada 46 dias de acordo com Azure Active Directory padrão. O AKS usa tipos de identidade gerenciados atribuídos pelo sistema e atribuídos pelo usuário. Atualmente, essas identidades são imutáveis. Para saber mais, leia sobre [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="before-you-begin"></a>Antes de começar

Você deve ter o seguinte recurso instalado:

- O CLI do Azure, versão 2.8.0 ou posterior

## <a name="limitations"></a>Limitações

* Não há suporte atualmente para trazer suas próprias identidades gerenciadas.
* Os clusters AKS com identidades gerenciadas podem ser habilitados somente durante a criação do cluster.
* Os clusters AKS existentes não podem ser atualizados ou atualizados para habilitar identidades gerenciadas.
* Durante as operações de **atualização** do cluster, a identidade gerenciada está temporariamente indisponível.

## <a name="summary-of-managed-identities"></a>Resumo de identidades gerenciadas

O AKS usa várias identidades gerenciadas para serviços e Complementos internos.

| Identidade                       | Nome    | Caso de uso | Permissões padrão | Traga sua própria identidade
|----------------------------|-----------|----------|
| Painel de controle | não visível | Usado pelo AKS para gerenciar recursos de rede, por exemplo, criar um balanceador de carga para entrada, IP público etc.| Função de colaborador para grupo de recursos de nó | Sem suporte no momento
| Kubelet | Nome do cluster AKS – agentpool | Autenticação com o ACR (registro de contêiner do Azure) | Função de leitor para grupo de recursos de nó | Sem suporte no momento
| Complemento | AzureNPM | Nenhuma identidade necessária | NA | Não
| Complemento | Monitoramento de rede AzureCNI | Nenhuma identidade necessária | NA | Não
| Complemento | azurepolicy (gatekeeper) | Nenhuma identidade necessária | NA | Não
| Complemento | azurepolicy | Nenhuma identidade necessária | NA | Não
| Complemento | Calico | Nenhuma identidade necessária | NA | Não
| Complemento | Dashboard | Nenhuma identidade necessária | NA | Não
| Complemento | HTTPApplicationRouting | Gerencia os recursos de rede necessários | Função de leitor para grupo de recursos de nó, função de colaborador para a zona DNS | Não
| Complemento | Gateway de aplicativo de entrada | Gerencia os recursos de rede necessários| Função de colaborador para grupo de recursos de nó | Não
| Complemento | omsagent | Usado para enviar métricas de AKS para Azure Monitor | Função de editor de métricas de monitoramento | Não
| Complemento | Nó virtual (ACIConnector) | Gerencia os recursos de rede necessários para ACI (instâncias de contêiner do Azure) | Função de colaborador para grupo de recursos de nó | Não


## <a name="create-an-aks-cluster-with-managed-identities"></a>Criar um cluster AKS com identidades gerenciadas

Agora você pode criar um cluster AKS com identidades gerenciadas usando os comandos da CLI a seguir.

Primeiro, crie um grupo de recursos do Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Em seguida, crie um cluster do AKS:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Uma criação de cluster bem-sucedida usando identidades gerenciadas contém estas informações de perfil de entidade de serviço:

```json
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Use o comando a seguir para consultar o ObjectID da sua identidade gerenciada do plano de controle:

```azurecli-interactive
az aks show -g myResourceGroup -n MyManagedCluster --query "identity"
```

O resultado deve ser semelhante a:

```json
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

> [!NOTE]
> Para criar e usar sua própria VNet, endereço IP estático ou disco do Azure anexado em que os recursos estão fora do grupo de recursos do nó de trabalho, use a entidade de segurança do sistema de cluster atribuída à identidade gerenciada para executar uma atribuição de função. Para obter mais informações sobre a atribuição de função, consulte [delegar acesso a outros recursos do Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> As concessões de permissão para a identidade gerenciada do cluster usada pelo provedor de nuvem do Azure podem levar de 60 minutos para popular.

Por fim, obtenha as credenciais para acessar o cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

O cluster será criado em alguns minutos. Em seguida, você pode implantar suas cargas de trabalho de aplicativo no novo cluster e interagir com ela da mesma forma como você fez com clusters AKS com base na entidade de serviço.

## <a name="next-steps"></a>Próximas etapas
* Use [modelos de Azure Resource Manager (ARM)][aks-arm-template] para criar clusters habilitados para identidade gerenciada.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
