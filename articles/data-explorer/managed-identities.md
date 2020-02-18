---
title: Como configurar identidades gerenciadas para o cluster de Data Explorer do Azure
description: Saiba como configurar identidades gerenciadas para o cluster de Data Explorer do Azure.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: e76ae2e072bb780ac9788902e9157db871e4f09d
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373379"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Configurar identidades gerenciadas para o cluster de Data Explorer do Azure

Uma [identidade gerenciada do Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) permite que o cluster acesse facilmente outros recursos protegidos por AAD, como Azure Key Vault. A identidade é gerenciada pela plataforma do Azure e não exige que você provisione ou gire segredos. Este artigo mostra como criar uma identidade gerenciada para clusters de Data Explorer do Azure. Atualmente, a configuração de identidade gerenciada tem suporte apenas para [habilitar chaves gerenciadas pelo cliente para o cluster](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault).

> [!Note]
> As identidades gerenciadas para o Azure Data Explorer não se comportarão conforme o esperado se seu aplicativo for migrado entre assinaturas ou locatários. O aplicativo precisará obter uma nova identidade, o que pode ser feito desabilitando e reabilitando o recurso usando [remover uma identidade](#remove-an-identity). As políticas de acesso dos recursos downstream também precisarão ser atualizadas para usar a nova identidade.

## <a name="add-a-system-assigned-identity"></a>Adicionar uma identidade atribuída pelo sistema

O cluster pode ser atribuído a uma **identidade atribuída pelo sistema** que esteja vinculada ao cluster e será excluído se o cluster for excluído. Um cluster só pode ter uma identidade atribuída pelo sistema. A criação de um cluster com uma identidade atribuída pelo sistema requer que uma propriedade adicional seja definida no cluster.

### <a name="add-a-system-assigned-identity-using-c"></a>Adicionar uma identidade atribuída pelo sistema usandoC#

Para configurar uma identidade gerenciada usando o cliente do C# Azure data Explorer, faça o seguinte:

* Instale o [pacote NuGet do Azure data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Instale o [pacote NuGet Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) para autenticação.
* Para executar o exemplo a seguir, [crie um aplicativo do Azure AD e uma](/azure/active-directory/develop/howto-create-service-principal-portal) entidade de serviço que possa acessar recursos. Você pode adicionar a atribuição de função no escopo da assinatura e obter os `Directory (tenant) ID`, `Application ID`e `Client Secret`necessários.

#### <a name="create-or-update-your-cluster"></a>Criar ou atualizar seu cluster

1. Crie ou atualize seu cluster usando a propriedade `Identity`:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
    
    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };
    
    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. Execute o seguinte comando para verificar se o cluster foi criado com êxito ou atualizado com uma identidade:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Se o resultado contiver `ProvisioningState` com o valor `Succeeded`, o cluster foi criado ou atualizado e deverá ter as seguintes propriedades:
   
    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

    `PrincipalId` e `TenantId` são substituídos por GUIDs. A propriedade `TenantId` identifica o locatário do AAD ao qual a identidade pertence. O `PrincipalId` é um identificador exclusivo para a nova identidade do cluster. No AAD, a entidade de serviço tem o mesmo nome que você deu à sua instância do Serviço de Aplicativo ou das Funções do Azure.

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Adicionar uma identidade atribuída pelo sistema usando um modelo de Azure Resource Manager

Um modelo do Azure Resource Manager pode ser usado para automatizar a implantação de recursos do Azure. Para saber mais sobre como implantar o Data Explorer do Azure, confira [criar um cluster de data Explorer do Azure e um banco de dados usando um modelo de Azure Resource Manager](create-cluster-database-resource-manager.md).

A adição do tipo atribuído pelo sistema informa ao Azure para criar e gerenciar a identidade do cluster. Qualquer recurso do tipo `Microsoft.Kusto/clusters` pode ser criado com uma identidade, incluindo a propriedade a seguir na definição de recurso: 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Por exemplo:

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

Quando o cluster é criado, ele tem as seguintes propriedades adicionais:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` e `<PRINCIPALID>` são substituídos por GUIDs. A propriedade `TenantId` identifica o locatário do AAD ao qual a identidade pertence. O `PrincipalId` é um identificador exclusivo para a nova identidade do cluster. No AAD, a entidade de serviço tem o mesmo nome que você deu à sua instância do Serviço de Aplicativo ou das Funções do Azure.

## <a name="remove-an-identity"></a>Remover uma identidade

A remoção de uma identidade atribuída pelo sistema também a excluirá do AAD. As identidades atribuídas pelo sistema também são removidas automaticamente do AAD quando o recurso de cluster é excluído. Uma identidade atribuída pelo sistema pode ser removida desabilitando o recurso:

```json
"identity": {
    "type": "None"
}
```

## <a name="next-steps"></a>Próximas etapas

* [Proteger clusters de Data Explorer do Azure no Azure](security.md)
* [Proteja seu cluster no Azure data Explorer-portal do Azure](manage-cluster-security.md) habilitando a criptografia em repouso.
 * [Configurar chaves gerenciadas pelo cliente usando oC#](customer-managed-keys-csharp.md)
 * [Configurar chaves gerenciadas pelo cliente usando o modelo de Azure Resource Manager](customer-managed-keys-resource-manager.md)
