---
title: Resource providers and resource types
description: Descreve os provedores de recursos que oferecem suporte ao Gerenciador de Recursos, aos respectivos esquemas e versões de API disponíveis, bem como às regiões que podem hospedar os recursos.
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 73cc053ab2ca19f42e3c45b8350d1e2baedfcc7a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422216"
---
# <a name="azure-resource-providers-and-types"></a>Provedores e tipos de recursos do Azure

Ao implantar recursos, com frequência você precisa recuperar informações sobre os provedores e tipos de recursos. For example, if you want to store keys and secrets, you work with the Microsoft.KeyVault resource provider. This resource provider offers a resource type called vaults for creating the key vault.

O nome de um tipo de recurso está no formato: **{provedor-de-recursos}/{tipo-de-recurso}** . O tipo de recurso de um cofre de chaves é **Microsoft.KeyVault/vaults**.

Neste artigo, você aprenderá a:

* Exibir todos os provedores de recursos no Azure
* Verificar o status do registro de um provedor de recursos
* Registrar um provedor de recursos
* Exibir os tipos de recurso para um provedor de recursos
* Exibir localizações válidas para um tipo de recurso
* Exibir versões de API válidas para um tipo de recurso

You can do these steps through the Azure portal, Azure PowerShell, or Azure CLI.

For a list that maps resource providers to Azure services, see [Resource providers for Azure services](azure-services-resource-providers.md).

## <a name="azure-portal"></a>Portal do Azure

Para ver todos os provedores de recursos e o status do registro para a sua assinatura:

1. Entre no [portal do Azure](https://portal.azure.com).
2. On the Azure portal menu, select **All services**.

    ![selecionar assinaturas](./media/resource-manager-supported-services/select-all-services.png)

3. Na caixa **Todos os serviços** , digite **assinatura** e, em seguida, selecione **Assinaturas**.
4. Selecione a assinatura na lista de assinaturas para visualizá-la.
5. Selecione **Provedores de recursos** e exiba a lista de provedores de recursos disponíveis.

    ![mostrar provedores de recursos](./media/resource-manager-supported-services/show-resource-providers.png)

6. O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. O escopo de registro é sempre a assinatura. Por padrão, muitos provedores de recursos são automaticamente registrados. No entanto, talvez seja necessário registrar manualmente alguns provedores de recursos. To register a resource provider, you must have permission to do the `/register/action` operation for the resource provider. Esta operação está incluída nas funções de Colaborador e de Proprietário. Para registrar um provedor de recursos, selecione **Registrar**. Na captura de tela anterior, o link **Registrar** é destacado para **Microsoft.Blueprint**.

    You can't unregister a resource provider when you still have resource types from that resource provider in your subscription.

Para obter informações para um provedor de recursos específico:

1. Entre no [portal do Azure](https://portal.azure.com).
2. On the Azure portal menu, select **All services**.
3. Na caixa **Todos os serviços**, digite **gerenciador de recursos** e, em seguida, selecione **Gerenciador de Recursos**.

    ![selecionar Todos os serviços](./media/resource-manager-supported-services/select-resource-explorer.png)

4. Expanda **provedores** selecionando a seta para a direita.

    ![Selecionar provedores](./media/resource-manager-supported-services/select-providers.png)

5. Expanda o provedor de recursos e o tipo de recurso que você deseja exibir.

    ![Selecionar tipo de recurso](./media/resource-manager-supported-services/select-resource-type.png)

6. O Gerenciador de Recursos tem suporte em todas as regiões, mas os recursos que você implanta talvez não tenham suporte em todas as regiões. Além disso, pode haver limitações em sua assinatura que impedem o uso de algumas regiões que oferecem suporte aos recursos. O Resource Explorer mostra localizações válidas para o tipo de recurso.

    ![Mostrar localizações](./media/resource-manager-supported-services/show-locations.png)

7. A versão disponível da API corresponde a uma versão das operações da API REST lançadas pelo provedor de recursos. Conforme um provedor de recursos habilita novos recursos, ele lança uma nova versão da API REST. O Resource Explorer mostra versões de API válidas para o tipo de recurso.

    ![Mostrar versões de API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para ver todos os provedores de recursos no Azure e o status do registro para a sua assinatura, use:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Que retorna resultados semelhantes a:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. O escopo de registro é sempre a assinatura. Por padrão, muitos provedores de recursos são automaticamente registrados. No entanto, talvez seja necessário registrar manualmente alguns provedores de recursos. To register a resource provider, you must have permission to do the `/register/action` operation for the resource provider. Esta operação está incluída nas funções de Colaborador e de Proprietário.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Que retorna resultados semelhantes a:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

You can't unregister a resource provider when you still have resource types from that resource provider in your subscription.

Para obter informações para um provedor de recursos específico, use:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Que retorna resultados semelhantes a:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Para ver os tipos de recurso para um provedor de recursos, use:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Que retorna:

```powershell
batchAccounts
operations
locations
locations/quotas
```

A versão disponível da API corresponde a uma versão das operações da API REST lançadas pelo provedor de recursos. Conforme um provedor de recursos habilita novos recursos, ele lança uma nova versão da API REST.

Para obter versões de API disponíveis para um tipo de recurso, use:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Que retorna:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

O Gerenciador de Recursos tem suporte em todas as regiões, mas os recursos que você implanta talvez não tenham suporte em todas as regiões. Além disso, pode haver limitações em sua assinatura que impedem o uso de algumas regiões que oferecem suporte aos recursos.

Para obter as localizações com suporte para um tipo de recurso, use.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Que retorna:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Para ver todos os provedores de recursos no Azure e o status do registro para a sua assinatura, use:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Que retorna resultados semelhantes a:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. O escopo de registro é sempre a assinatura. Por padrão, muitos provedores de recursos são automaticamente registrados. No entanto, talvez seja necessário registrar manualmente alguns provedores de recursos. To register a resource provider, you must have permission to do the `/register/action` operation for the resource provider. Esta operação está incluída nas funções de Colaborador e de Proprietário.

```azurecli
az provider register --namespace Microsoft.Batch
```

Que retorna uma mensagem de que o registro está em andamento.

You can't unregister a resource provider when you still have resource types from that resource provider in your subscription.

Para obter informações para um provedor de recursos específico, use:

```azurecli
az provider show --namespace Microsoft.Batch
```

Que retorna resultados semelhantes a:

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Para ver os tipos de recurso para um provedor de recursos, use:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Que retorna:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

A versão disponível da API corresponde a uma versão das operações da API REST lançadas pelo provedor de recursos. Conforme um provedor de recursos habilita novos recursos, ele lança uma nova versão da API REST.

Para obter versões de API disponíveis para um tipo de recurso, use:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Que retorna:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

O Gerenciador de Recursos tem suporte em todas as regiões, mas os recursos que você implanta talvez não tenham suporte em todas as regiões. Além disso, pode haver limitações em sua assinatura que impedem o uso de algumas regiões que oferecem suporte aos recursos.

Para obter as localizações com suporte para um tipo de recurso, use.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Que retorna:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Próximos passos

* Para saber mais sobre a criação de modelos do Gerenciador de Recursos, confira [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md). 
* Para exibir os esquemas de modelo de provedor de recursos, confira [Referência de modelo](/azure/templates/).
* For a list that maps resource providers to Azure services, see [Resource providers for Azure services](azure-services-resource-providers.md).
* Para exibir as operações para um provedor de recursos, consulte [API REST do Azure](/rest/api/).
