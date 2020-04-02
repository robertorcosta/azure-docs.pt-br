---
title: Como configurar identidades gerenciadas para o cluster Azure Data Explorer
description: Saiba como configurar identidades gerenciadas para o cluster Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 900bf815917a4b7c9841860d663a2183b1ab71b3
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529667"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Configure identidades gerenciadas para o cluster Azure Data Explorer

Uma [identidade gerenciada do Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) permite que seu cluster acesse facilmente outros recursos protegidos por AAD, como o Azure Key Vault. A identidade é gerenciada pela plataforma Azure e não exige que você provisão ou gire quaisquer segredos. Este artigo mostra como criar uma identidade gerenciada para clusters do Azure Data Explorer. A configuração de identidade gerenciada é suportada atualmente apenas para [habilitar chaves gerenciadas pelo cliente para o cluster](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault).

> [!Note]
> As identidades gerenciadas do Azure Data Explorer não se comportarão como esperado se o cluster Do Azure Data Explorer for migrado entre assinaturas ou inquilinos. O aplicativo precisará obter uma nova identidade, o que pode ser feito [desativando](#disable-a-system-assigned-identity) e [reativando](#add-a-system-assigned-identity) o recurso. As políticas de acesso aos recursos a jusante também precisarão ser atualizadas para usar a nova identidade.

## <a name="add-a-system-assigned-identity"></a>Adicionar uma identidade atribuída ao sistema
                                                                                                    
Atribuir uma identidade atribuída ao sistema que está vinculada ao seu cluster e é excluído se o cluster for excluído. Um cluster só pode ter uma identidade atribuída ao sistema. Criar um cluster com uma identidade atribuída ao sistema requer que uma propriedade adicional seja definida no cluster. A identidade atribuída ao sistema é adicionada usando C#, modelos ARM ou o portal Azure conforme detalhado abaixo.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Adicione uma identidade atribuída ao sistema usando o portal Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

#### <a name="new-azure-data-explorer-cluster"></a>Novo cluster Azure Data Explorer

1. [Crie um cluster Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
1. Na guia **Segurança** > **Sistema atribuído identidade, selecione** **Em**. Para remover a identidade atribuída ao sistema, selecione **Desfazer**.
2. Selecione **Next:Tags>** ou **Review + create** para criar o cluster.

    ![Adicionar identidade atribuída ao sistema ao novo cluster](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>Cluster Azure Data Explorer existente

1. Abra um cluster azure Data Explorer existente.
1. Selecione **Configurações** > **Identidade** no painel esquerdo do portal.
1. Na guia **'Sistema** > painel de **identidade' atribuído:**
   1. Mova o controle deslizante **de status** para **On**.
   1. Selecione **Salvar**
   1. Na janela pop-up, selecione **Sim**

    ![Adicionar identidade atribuída ao sistema](media/managed-identities/turn-system-assigned-identity-on.png)

1. Depois de alguns minutos, a tela mostra: 
  * **ID de objeto** - usado para chaves gerenciadas pelo cliente 
  * **Atribuições de funções** - clique em link para atribuir funções relevantes

    ![Identidade atribuída ao sistema em](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>Adicionar uma identidade atribuída ao sistema usando C #

#### <a name="prerequisites"></a>Pré-requisitos

Para configurar uma identidade gerenciada usando o cliente Azure Data Explorer C#:

* Instale o pacote NuGet do [Azure Data Explorer (Kusto).](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)
* Instale o [pacote Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) para autenticação.
* [Crie um aplicativo ad](/azure/active-directory/develop/howto-create-service-principal-portal) e um princípio de serviço do Azure que possam acessar recursos. Você adiciona atribuição de função no `Directory (tenant) ID` `Application ID`escopo `Client Secret`da assinatura e recebe o necessário, e .

#### <a name="create-or-update-your-cluster"></a>Crie ou atualize seu cluster

1. Crie ou atualize `Identity` seu cluster usando a propriedade:

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
    
2. Execute o seguinte comando para verificar se o cluster foi criado ou atualizado com sucesso com uma identidade:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Se o `ProvisioningState` resultado `Succeeded` contiver o valor, o cluster foi criado ou atualizado e deve ter as seguintes propriedades:

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId`e `TenantId` são substituídos por GUIDs. A `TenantId` propriedade identifica o inquilino AAD ao qual a identidade pertence. O `PrincipalId` é um identificador único para a nova identidade do cluster. No AAD, a entidade de serviço tem o mesmo nome que você deu à sua instância do Serviço de Aplicativo ou das Funções do Azure.

# <a name="arm-template"></a>[Modelo ARM](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Adicione uma identidade atribuída ao sistema usando um modelo do Azure Resource Manager

Um modelo do Azure Resource Manager pode ser usado para automatizar a implantação de recursos do Azure. Para saber mais sobre a implantação do Azure Data Explorer, consulte [Criar um cluster e banco de dados do Azure Data Explorer usando um modelo do Azure Resource Manager](create-cluster-database-resource-manager.md).

A adição do tipo atribuído ao sistema diz ao Azure para criar e gerenciar a identidade do seu cluster. Qualquer recurso do tipo `Microsoft.Kusto/clusters` pode ser criado com uma identidade, incluindo a propriedade a seguir na definição de recurso: 

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

`<TENANTID>`e `<PRINCIPALID>` são substituídos por GUIDs. A `TenantId` propriedade identifica o inquilino AAD ao qual a identidade pertence. O `PrincipalId` é um identificador único para a nova identidade do cluster. No AAD, a entidade de serviço tem o mesmo nome que você deu à sua instância do Serviço de Aplicativo ou das Funções do Azure.

---

## <a name="disable-a-system-assigned-identity"></a>Desativar uma identidade atribuída ao sistema

A remoção de uma identidade atribuída ao sistema também irá excluí-la do AAD. As identidades atribuídas ao sistema também são automaticamente removidas do AAD quando o recurso de cluster é excluído. Uma identidade atribuída ao sistema pode ser removida desativando o recurso.  A identidade atribuída ao sistema é removida usando c#, modelos ARM ou o portal Azure conforme detalhado abaixo.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

### <a name="disable-a-system-assigned-identity-using-the-azure-portal"></a>Desativar uma identidade atribuída ao sistema usando o portal Azure

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Selecione **Configurações** > **Identidade** no painel esquerdo do portal.
1. Na guia **'Sistema** > painel de **identidade' atribuído:**
    1. Mova o controle deslizante **de status** para **desligado**.
    1. Selecione **Salvar**
    1. Na janela pop-up, selecione **Sim** para desativar a identidade atribuída ao sistema. O painel **Identidade** reverte para a mesma condição de antes da adição da identidade atribuída ao sistema.

    ![Sistema atribuído identidade off](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>Remova uma identidade atribuída ao sistema usando C #

Execute o seguinte para remover a identidade atribuída ao sistema:

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[Modelo ARM](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Remova uma identidade atribuída ao sistema usando um modelo do Azure Resource Manager

Execute o seguinte para remover a identidade atribuída ao sistema:

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>Próximas etapas

* [Grupos secure Azure Data Explorer no Azure](security.md)
* [Proteja seu cluster no Azure Data Explorer - portal Azure,](manage-cluster-security.md) ativando a criptografia em repouso.
 * [Configure as chaves gerenciadas pelo cliente usando C #](customer-managed-keys-csharp.md)
 * [Configure as chaves gerenciadas pelo cliente usando o modelo do Azure Resource Manager](customer-managed-keys-resource-manager.md)
