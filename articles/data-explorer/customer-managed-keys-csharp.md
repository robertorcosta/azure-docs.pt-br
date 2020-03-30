---
title: 'Configure as chaves gerenciadas pelo cliente usando C #'
description: Este artigo descreve como configurar a criptografia de chaves gerenciadas pelo cliente em seus dados no Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a00b0876c4a188b932032129ed5a394e94198930
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297957"
---
# <a name="configure-customer-managed-keys-using-c"></a>Configure as chaves gerenciadas pelo cliente usando C #

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Modelo de Gerenciador de recursos do Azure](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar criptografia com chaves gerenciadas pelo cliente

Esta seção mostra como configurar a criptografia de chaves gerenciadas pelo cliente usando o cliente Azure Data Explorer C#. 

### <a name="prerequisites"></a>Pré-requisitos

* Se você não tem o Visual 2019 Studio instalado, baixe e use o **Visual Studio 2019 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Certifique-se de que você **habilite o desenvolvimento do Azure** durante a configuração do Visual Studio.

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

### <a name="install-c-nuget"></a>Instalar C# NuGet

* Instale o pacote NuGet do [Azure Data Explorer (Kusto).](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)

* Instale o [pacote Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) para autenticação.

### <a name="authentication"></a>Autenticação

Para executar os exemplos deste artigo, [crie um aplicativo Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) e um princípio de serviço que possa acessar recursos. Você pode adicionar atribuição de função `Directory (tenant) ID`no `Application ID`escopo `Client Secret`da assinatura e obter o necessário , e .

### <a name="configure-cluster"></a>Configurar cluster

Por padrão, a criptografia do Azure Data Explorer usa chaves gerenciadas pela Microsoft. Configure o cluster Azure Data Explorer para usar as chaves gerenciadas pelo cliente e especifique a chave para associar ao cluster.

1. Atualize seu cluster usando o seguinte código:

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
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. Execute o seguinte comando para verificar se o cluster foi atualizado com sucesso:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Se o `ProvisioningState` resultado `Succeeded` contiver o valor, seu cluster foi atualizado com sucesso.

## <a name="update-the-key-version"></a>Atualize a versão-chave

Quando você criar uma nova versão de uma chave, você precisará atualizar o cluster para usar a nova versão. Primeiro, `Get-AzKeyVaultKey` ligue para obter a versão mais recente da chave. Em seguida, atualize as propriedades do cofre chave do cluster para usar a nova versão da chave, conforme mostrado no [cluster Configurar](#configure-cluster).

## <a name="next-steps"></a>Próximas etapas

* [Grupos secure Azure Data Explorer no Azure](security.md)
* [Configure identidades gerenciadas para o cluster Azure Data Explorer](managed-identities.md)
* [Proteja seu cluster no Azure Data Explorer - portal Azure,](manage-cluster-security.md) ativando a criptografia em repouso.
* [Configure as chaves gerenciadas pelo cliente usando o modelo do Azure Resource Manager](customer-managed-keys-resource-manager.md)


