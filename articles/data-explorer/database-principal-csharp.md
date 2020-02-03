---
title: Adicionar entidades de banco de dados para Data Explorer do Azure usandoC#
description: Neste artigo, você aprenderá a adicionar entidades de banco de dados para o Azure Data Explorer C#usando o.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965028"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Adicionar entidades de banco de dados para Data Explorer do Azure usandoC#

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Modelo do Azure Resource Manager](database-principal-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. Neste artigo, você adiciona entidades de banco de dados para o Azure Data Explorer C#usando o.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver o Visual Studio 2019 instalado, poderá baixar e usar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)gratuito. Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.
* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* [Crie um cluster e um banco de dados](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Instalar C# o NuGet

* Instale [Microsoft. Azure. Management. Kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Instale [Microsoft. REST. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) para autenticação.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Adicionar uma entidade de banco de dados

O exemplo a seguir mostra como adicionar uma entidade de segurança de banco de dados programaticamente.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
string principalAssignmentName = "databasePrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "Admin";//Admin, Ingestor, Monitor, User, UnrestrictedViewers, Viewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var databasePrincipalAssignment = new DatabasePrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.DatabasePrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, principalAssignmentName, databasePrincipalAssignment);
```

|**Configuração** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID do locatário. Também conhecida como ID de diretório.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A ID da assinatura que você usa para a criação de recursos.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A ID do cliente do aplicativo que pode acessar recursos em seu locatário.|
| clientSecret | *xxxxxxxxxxxxxx* | O segredo do cliente do aplicativo que pode acessar recursos em seu locatário. |
| resourceGroupName | *testrg* | O nome do grupo de recursos que contém o cluster.|
| clusterName | *mykustocluster* | O nome do cluster.|
| databaseName | *mykustodatabase* | O nome do banco de dados.|
| principalAssignmentName | *databasePrincipalAssignment1* | O nome do recurso principal do banco de dados.|
| principalId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A ID da entidade, que pode ser email do usuário, ID do aplicativo ou nome do grupo de segurança.|
| função | *Administrador* | A função da entidade de segurança do banco de dados, que pode ser ' admin ', ' ingeritor ', ' monitor ', ' user ', ' UnrestrictedViewers ', ' Viewer '.|
| tenantIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A ID do locatário da entidade de segurança.|
| PrincipalType | *Aplicativo* | O tipo de entidade de segurança, que pode ser ' user ', ' app ' ou ' Group '|

## <a name="next-steps"></a>Próximos passos

* [Ingerir dados usando a biblioteca do Node do Azure Data Explorer](node-ingest-data.md)
