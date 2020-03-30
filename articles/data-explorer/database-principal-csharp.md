---
title: 'Adicionar os princípios de banco de dados para o Azure Data Explorer usando C #'
description: Neste artigo, você aprende como adicionar os principais do banco de dados para o Azure Data Explorer usando C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965028"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Adicionar os princípios de banco de dados para o Azure Data Explorer usando C #

> [!div class="op_single_selector"]
> * [C #](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Modelo de Gerenciador de recursos do Azure](database-principal-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. Neste artigo, você adiciona os princípios do banco de dados do Azure Data Explorer usando C#.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tem o Visual 2019 Studio instalado, baixe e use o **Visual Studio 2019 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Certifique-se de que você **habilite o desenvolvimento do Azure** durante a configuração do Visual Studio.
* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* [Crie um cluster e um banco de dados](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Instalar C# NuGet

* Instale [o Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Instale [microsoft.rest.clientRuntime.Azure.Autenticação](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) para autenticação.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Adicionar um principal de banco de dados

O exemplo a seguir mostra como adicionar um banco de dados principal programáticamente.

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
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID do locatário. Também conhecido como ID do diretório.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID de assinatura que você usa para criação de recursos.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID do cliente do aplicativo que pode acessar recursos em seu inquilino.|
| clientSecret | *xxxxxxxxxxxxxx* | O segredo do cliente do aplicativo que pode acessar recursos em seu inquilino. |
| resourceGroupName | *testrg* | O nome do grupo de recursos que contém seu cluster.|
| clusterName | *mykustocluster* | O nome do seu grupo.|
| databaseName | *mykustodatabase* | O nome do banco de dados.|
| principalNome de atribuição | *banco de dadosPrincipalAssignment1* | O nome do seu principal recurso de banco de dados.|
| principalId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID principal, que pode ser e-mail do usuário, ID do aplicativo ou nome do grupo de segurança.|
| função | *Administração* | O papel do principal do seu banco de dados, que pode ser 'Administrador', 'Ingestor', 'Monitor', 'Usuário', 'Visualizadores irrestritos', 'Visualizador'.|
| inquilinoIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A id do inquilino do diretor.|
| principalType | *Aplicativo* | O tipo do principal, que pode ser 'Usuário', 'App' ou 'Grupo'|

## <a name="next-steps"></a>Próximas etapas

* [Ingerir dados usando a biblioteca Dode do Explorador de Dados do Azure](node-ingest-data.md)
