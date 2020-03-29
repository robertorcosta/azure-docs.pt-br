---
title: Adicionar princípios de cluster para o Azure Data Explorer usando python
description: Neste artigo, você aprende a adicionar os principais de cluster para o Azure Data Explorer usando python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 637efdfe31d1f2eb0eaa5dd532dd9e9e67de5ce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965132"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-python"></a>Adicionar princípios de cluster para o Azure Data Explorer usando python

> [!div class="op_single_selector"]
> * [C #](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Modelo de Gerenciador de recursos do Azure](cluster-principal-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. Neste artigo, você adiciona os princípios de cluster para o Azure Data Explorer usando Python.

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* [Crie um cluster](create-cluster-database-python.md).

## <a name="install-python-package"></a>Instalar o pacote do Python

Para instalar o pacote Python para o Azure Data Explorer (Kusto), abra um prompt de comando que tenha Python no caminho. Execute este comando:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Adicionar um principal de cluster

O exemplo a seguir mostra como adicionar um cluster principal programáticamente.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import ClusterPrincipalAssignment
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
#The cluster that is created as part of the Prerequisites
cluster_name = "mykustocluster"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.cluster_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, principal_assignment_name= principal_assignment_name, parameters=ClusterPrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Configuração** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID do locatário. Também conhecido como ID do diretório.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID de assinatura que você usa para criação de recursos.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID do cliente do aplicativo que pode acessar recursos em seu inquilino.|
| client_secret | *xxxxxxxxxxxxxx* | O segredo do cliente do aplicativo que pode acessar recursos em seu inquilino. |
| resource_group_name | *testrg* | O nome do grupo de recursos que contém seu cluster.|
| cluster_name | *mykustocluster* | O nome do seu grupo.|
| principal_assignment_name | *clusterPrincipalAssignment1* | O nome do seu recurso principal de cluster.|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID principal, que pode ser e-mail do usuário, ID do aplicativo ou nome do grupo de segurança.|
| função | *Todos os bancos de dadosAdmin* | A função do seu principal de cluster, que pode ser 'AllDatabasesAdmin' ou 'AllDatabasesViewer'.|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A id do inquilino do diretor.|
| principal_type | *Aplicativo* | O tipo do principal, que pode ser 'Usuário', 'App' ou 'Grupo'|

## <a name="next-steps"></a>Próximas etapas

* [Adicionar diretores de banco de dados](database-principal-python.md)
