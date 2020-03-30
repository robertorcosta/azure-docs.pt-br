---
title: Identidade gerenciada para o Data Factory
description: Saiba mais sobre a identidade gerenciada para a Fábrica de Dados Azure.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: 45699680ad2003c034bce588857f8b102a0b6d26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261119"
---
# <a name="managed-identity-for-data-factory"></a>Identidade gerenciada para o Data Factory

Este artigo ajuda você a entender o que é identidade gerenciada para a Fábrica de Dados (anteriormente conhecida como Identidade de Serviço Gerenciado/MSI) e como ela funciona.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral

Ao criar uma fábrica de dados, uma identidade gerenciada pode ser criada juntamente com a criação de fábrica. A identidade gerenciada é um aplicativo gerenciado registrado no Azure Active Directory e representa essa fábrica de dados específica.

A identidade gerenciada para a Fábrica de Dados beneficia os seguintes recursos:

- [Armazenar credencial no Azure Key Vault](store-credentials-in-key-vault.md), nesse caso, a identidade gerenciada pela fábrica de dados é usada para autenticação do Azure Key Vault.
- Conectores incluindo [armazenamento de Blobs do Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Banco de Dados SQL do Azure](connector-azure-sql-database.md), e [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Atividade web](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Gerar identidade gerenciada

A identidade gerenciada para fábrica de dados é gerada da seguinte forma:

- Ao criar a fábrica de dados através **do portal Azure ou do PowerShell,** a identidade gerenciada sempre será criada automaticamente.
- Ao criar a fábrica de dados através do **SDK,** a identidade gerenciada será criada somente se você especificar "Identidade = nova Identidade de Fábrica()" no objeto de fábrica para criação. Consulte o exemplo do [início rápido do .NET - criar data factory](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Ao criar a fábrica de dados através **da API REST,** a identidade gerenciada será criada somente se você especificar a seção "identidade" no órgão de solicitação. Consulte o exemplo do [início rápido do REST - criar data factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Se você descobrir que sua fábrica de dados não tem uma identidade gerenciada associada após a instrução [de identidade gerenciada de recuperação,](#retrieve-managed-identity) você pode gerar explicitamente uma atualizando a fábrica de dados com iniciador de identidade programáticamente:

- [Gerar identidade gerenciada usando o PowerShell](#generate-managed-identity-using-powershell)
- [Gerar identidade gerenciada usando a API REST](#generate-managed-identity-using-rest-api)
- [Gerar identidade gerenciada usando um modelo do Azure Resource Manager](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Gerar identidade gerenciada usando O SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- A identidade gerenciada não pode ser modificada. Atualizar uma fábrica de dados que já tenha uma identidade gerenciada não terá nenhum impacto, a identidade gerenciada é mantida inalterada.
>- Se você atualizar uma fábrica de dados que já tenha uma identidade gerenciada sem especificar parâmetro de "identidade" no objeto de fábrica ou sem especificar a seção "identidade" no órgão de solicitação REST, você terá um erro.
>- Quando você excluir uma fábrica de dados, a identidade gerenciada associada será excluída junto.

### <a name="generate-managed-identity-using-powershell"></a>Gerar identidade gerenciada usando o PowerShell

Chamar o comando **Set-AzDataFactoryV2** novamente, e então você verá campos "Identidade" sendo gerados recentemente:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Gerar identidade gerenciada usando a API REST

Chame a API abaixo com a seção "identity" no corpo da solicitação:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Corpo da solicitação**: adicionar "identity": {"type": "SystemAssigned"}.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Resposta**: a identidade gerenciada é criada automaticamente e a seção "identidade" é preenchida de acordo.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Gerar identidade gerenciada usando um modelo do Azure Resource Manager

**Modelo**: adicionar "identity": {"type": "SystemAssigned"}.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Gerar identidade gerenciada usando O SDK

Chame a função create_or_update do data factory com Identity=new FactoryIdentity(). Exemplo de código usando .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Recuperar identidade gerenciada

Você pode recuperar a identidade gerenciada do portal Azure ou programáticamente. As seções a seguir mostram alguns exemplos.

>[!TIP]
> Se você não ver a identidade gerenciada, [gere identidade gerenciada](#generate-managed-identity) atualizando sua fábrica.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Recuperar identidade gerenciada usando o portal Azure

Você pode encontrar as informações de identidade gerenciadas do portal Azure - > sua fábrica de dados - propriedades >.

- ID do objeto de identidade gerenciado
- Inquilino de identidade gerenciada
- ID de aplicativo de identidade gerenciado

As informações de identidade gerenciadas também aparecerão quando você criar um serviço vinculado que suporta autenticação de identidade gerenciada, como Azure Blob, Azure Data Lake Storage, Azure Key Vault, etc.

Ao conceder permissão, use o ID do objeto ou o nome de fábrica de dados (como nome de identidade gerenciado) para encontrar essa identidade.

### <a name="retrieve-managed-identity-using-powershell"></a>Recuperar identidade gerenciada usando o PowerShell

O ID principal de identidade gerenciado e o ID do inquilino serão devolvidos quando você tiver uma fábrica de dados específica da seguinte forma. Use o **PrincipalId** para conceder acesso:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Você pode obter o ID do aplicativo copiando acima do ID principal e, em seguida, executando abaixo do comando Azure Active Directory com iD principal como parâmetro.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Próximas etapas
Veja os seguintes tópicos que introduzem quando e como usar a identidade gerenciada pela fábrica de dados:

- [Armazenar credencial no Azure Key Vault](store-credentials-in-key-vault.md)
- [Copiar dados de/para o Azure Data Lake Store usando identidades gerenciadas para autenticação de recursos do Azure](connector-azure-data-lake-store.md)

Consulte [Identidades gerenciadas para visão geral dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para obter mais informações sobre identidades gerenciadas para os recursos do Azure, na qual a identidade gerenciada pela fábrica de dados é baseada. 
