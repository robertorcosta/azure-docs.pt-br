---
title: Usar identidades gerenciadas no serviço kubernetes do Azure
description: Saiba como usar identidades gerenciadas no serviço kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 07/17/2020
ms.author: thomasge
ms.openlocfilehash: 8c5c4a6e5d8b2997d80c7263ba17a705d3846ed8
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987385"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Usar identidades gerenciadas no serviço kubernetes do Azure

Atualmente, um cluster AKS (serviço de kubernetes do Azure) (especificamente, o provedor de nuvem kubernetes) requer uma identidade para criar recursos adicionais, como balanceadores de carga e discos gerenciados no Azure. Essa identidade pode ser uma *identidade gerenciada* ou uma *entidade de serviço*. Se você usar uma [entidade de serviço](kubernetes-service-principal.md), deverá fornecer uma ou AKs cria uma em seu nome. Se você usar a identidade gerenciada, ela será criada para você pelo AKS automaticamente. Os clusters que usam entidades de serviço eventualmente atingem um estado no qual a entidade de serviço deve ser renovada para manter o cluster funcionando. O gerenciamento de entidades de serviço adiciona complexidade, motivo pelo qual é mais fácil usar identidades gerenciadas. Os mesmos requisitos de permissão se aplicam a entidades de serviço e identidades gerenciadas.

*Identidades gerenciadas* são essencialmente um wrapper em relação às entidades de serviço e tornam seu gerenciamento mais simples. A rotação de credenciais para MI ocorre automaticamente a cada 46 dias de acordo com Azure Active Directory padrão. O AKS usa tipos de identidade gerenciados atribuídos pelo sistema e atribuídos pelo usuário. Atualmente, essas identidades são imutáveis. Para saber mais, leia sobre [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="before-you-begin"></a>Antes de começar

Você deve ter o seguinte recurso instalado:

- O CLI do Azure, versão 2.8.0 ou posterior

## <a name="limitations"></a>Limitações

* Os clusters AKS com identidades gerenciadas podem ser habilitados somente durante a criação do cluster.
* Os clusters AKS existentes não podem ser migrados para identidades gerenciadas.
* Durante as operações de **atualização** do cluster, a identidade gerenciada está temporariamente indisponível.
* Não há suporte para a movimentação/migração de locatários de clusters habilitados para identidade gerenciada.

## <a name="summary-of-managed-identities"></a>Resumo de identidades gerenciadas

O AKS usa várias identidades gerenciadas para serviços e Complementos internos.

| Identidade                       | Nome    | Caso de uso | Permissões padrão | Traga sua própria identidade
|----------------------------|-----------|----------|
| Painel de controle | não visível | Usado pelo AKS para recursos de rede gerenciados, incluindo balanceadores de carga de entrada e IPs públicos gerenciados por AKS | Função de colaborador para grupo de recursos de nó | Visualização
| Kubelet | Nome do cluster AKS – agentpool | Autenticação com o ACR (registro de contêiner do Azure) | Função de leitor para grupo de recursos de nó | Sem suporte no momento
| Complemento | AzureNPM | Nenhuma identidade necessária | NA | Não
| Complemento | Monitoramento de rede AzureCNI | Nenhuma identidade necessária | NA | Não
| Complemento | azurepolicy (gatekeeper) | Nenhuma identidade necessária | NA | Não
| Complemento | azurepolicy | Nenhuma identidade necessária | NA | Não
| Complemento | Calico | Nenhuma identidade necessária | NA | Não
| Complemento | Painel | Nenhuma identidade necessária | NA | Não
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

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Use o comando a seguir para consultar o ObjectID da sua identidade gerenciada do plano de controle:

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

O resultado deve ser semelhante a:

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

Depois que o cluster é criado, você pode implantar suas cargas de trabalho de aplicativo no novo cluster e interagir com ela da mesma forma como você fez com clusters de AKS com base na entidade de serviço.

> [!NOTE]
> Para criar e usar sua própria VNet, endereço IP estático ou disco do Azure anexado em que os recursos estão fora do grupo de recursos do nó de trabalho, use a entidade de segurança do sistema de cluster atribuída à identidade gerenciada para executar uma atribuição de função. Para obter mais informações sobre a atribuição de função, consulte [delegar acesso a outros recursos do Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> As concessões de permissão para a identidade gerenciada do cluster usada pelo provedor de nuvem do Azure podem levar de 60 minutos para popular.

Por fim, obtenha as credenciais para acessar o cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="bring-your-own-control-plane-mi-preview"></a>Traga seu próprio plano de controle MI (visualização)
Uma identidade de plano de controle personalizado permite que o acesso seja concedido à identidade existente antes da criação do cluster. Isso permite cenários como usar uma VNET personalizada ou uma saída de UDR com uma identidade gerenciada.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Você deve ter os seguintes recursos instalados:
- O CLI do Azure, versão 2.9.0 ou posterior
- A extensão 0.4.57 AKs-Preview

Limitações para trazer seu próprio plano de controle MI (visualização):
* Atualmente, o Azure governamental não tem suporte.
* Atualmente, não há suporte para o Azure China 21Vianet.

```azurecli-interactive
az extension add --name aks-preview
az extension list
```

```azurecli-interactive
az extension update --name aks-preview
az extension list
```

```azurecli-interactive
az feature register --name UserAssignedIdentityPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o status seja exibido como **Registrado**. Você pode verificar o status de registro usando o comando [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list):

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UserAssignedIdentityPreview')].{Name:name,State:properties.state}"
```

Quando o status aparecer como registrado, atualize o registro do provedor de recursos `Microsoft.ContainerService` usando o comando [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register):

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Se você ainda não tiver uma identidade gerenciada, deverá continuar e criar uma por exemplo usando [AZ Identity CLI][az-identity-create].

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
O resultado deve ser semelhante a:

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Se sua identidade gerenciada fizer parte de sua assinatura, você poderá usar o [comando AZ Identity CLI][az-identity-list] para consultá-la.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

Agora você pode usar o seguinte comando para criar o cluster com sua identidade existente:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

Uma criação de cluster bem-sucedida usando suas próprias identidades gerenciadas contém estas informações de perfil userAssignedIdentities:

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="next-steps"></a>Próximas etapas
* Use [modelos de Azure Resource Manager (ARM)][aks-arm-template] para criar clusters habilitados para identidade gerenciada.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-list]: /cli/azure/identity?view=azure-cli-latest#az-identity-list
