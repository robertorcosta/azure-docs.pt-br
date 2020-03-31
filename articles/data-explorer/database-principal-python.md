---
title: Adicionar princípios de banco de dados para o Azure Data Explorer usando python
description: Neste artigo, você aprende como adicionar os principais do banco de dados para o Azure Data Explorer usando python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 8b9c4f4d5427b326c273558db0bff808068b192a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965002"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>Adicionar princípios de banco de dados para o Azure Data Explorer usando python

> [!div class="op_single_selector"]
> * [C #](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Modelo de Gerenciador de recursos do Azure](database-principal-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. Neste artigo, você adiciona os princípios do banco de dados do Azure Data Explorer usando python.

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* [Criar um cluster em um banco de dados](create-cluster-database-python.md)

## <a name="install-python-package"></a>Instalar o pacote do Python

Para instalar o pacote Python para o Azure Data Explorer (Kusto), abra um prompt de comando que tenha Python no caminho. Execute este comando:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Adicionar um principal de banco de dados

O exemplo a seguir mostra como adicionar um banco de dados principal programáticamente.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipalAssignment
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that is created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.database_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, principal_assignment_name= principal_assignment_name, parameters=DatabasePrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Configuração** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID do locatário. Também conhecido como ID do diretório.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID de assinatura que você usa para criação de recursos.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID do cliente do aplicativo que pode acessar recursos em seu inquilino.|
| client_secret | *xxxxxxxxxxxxxx* | O segredo do cliente do aplicativo que pode acessar recursos em seu inquilino. |
| resource_group_name | *testrg* | O nome do grupo de recursos que contém seu cluster.|
| cluster_name | *mykustocluster* | O nome do seu grupo.|
| database_name | *mykustodatabase* | O nome do banco de dados.|
| principal_assignment_name | *banco de dadosPrincipalAssignment1* | O nome do seu principal recurso de banco de dados.|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID principal, que pode ser e-mail do usuário, ID do aplicativo ou nome do grupo de segurança.|
| função | *Administração* | O papel do principal do seu banco de dados, que pode ser 'Administrador', 'Ingestor', 'Monitor', 'Usuário', 'Visualizadores irrestritos', 'Visualizador'.|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A id do inquilino do diretor.|
| principal_type | *Aplicativo* | O tipo do principal, que pode ser 'Usuário', 'App' ou 'Grupo'|

## <a name="next-steps"></a>Próximas etapas

* [Ingerir dados usando a biblioteca Python do Azure Data Explorer](python-ingest-data.md)
