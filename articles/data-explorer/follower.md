---
title: Use o recurso de banco de dados de seguidores para anexar bancos de dados no Azure Data Explorer
description: Saiba como anexar bancos de dados no Azure Data Explorer usando o recurso de banco de dados de seguidores.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: f6dbdb54c1c5a5d477c3ccb988963758faab83b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79140007"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Use o banco de dados de seguidores para anexar bancos de dados no Azure Data Explorer

O recurso **de banco de dados de seguidores** permite anexar um banco de dados localizado em um cluster diferente ao seu cluster Azure Data Explorer. O **banco de dados de seguidores** é anexado no modo somente *leitura,* possibilitando visualizar os dados e executar consultas sobre os dados que foram ingeridos no **banco de dados do líder**. O banco de dados de seguidores sincroniza alterações nos bancos de dados de líderes. Devido à sincronização, há um atraso de dados de alguns segundos a poucos minutos na disponibilidade de dados. O tempo de atraso depende do tamanho geral dos metadados do banco de dados líder. Os bancos de dados líder e seguidor usam a mesma conta de armazenamento para buscar os dados. O armazenamento pertence ao banco de dados do líder. O banco de dados de seguidores visualiza os dados sem precisar ingeri-los. Como o banco de dados anexado é um banco de dados somente leitura, os dados, tabelas e políticas no banco de dados não podem ser modificados, exceto para [a política de cache,](#configure-caching-policy) [princípios](#manage-principals)e [permissões.](#manage-permissions) Bancos de dados anexados não podem ser excluídos. Eles devem ser destacados pelo líder ou seguidor e só então eles podem ser excluídos. 

Anexar um banco de dados a um cluster diferente usando o recurso de seguidores é usado como infra-estrutura para compartilhar dados entre organizações e equipes. O recurso é útil para segregar recursos de computação para proteger um ambiente de produção de casos de uso não-produzido. O Follower também pode ser usado para associar o custo do cluster Azure Data Explorer à parte que executa consultas nos dados.

## <a name="which-databases-are-followed"></a>Quais bancos de dados são seguidos?

* Um cluster pode seguir um banco de dados, vários bancos de dados ou todos os bancos de dados de um cluster líder. 
* Um único cluster pode seguir bancos de dados de vários clusters líderes. 
* Um cluster pode conter bancos de dados de seguidores e bancos de dados de líderes

## <a name="prerequisites"></a>Pré-requisitos

1. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
1. [Crie cluster e DB](/azure/data-explorer/create-cluster-database-portal) para o líder e seguidor.
1. [Ingerir dados](/azure/data-explorer/ingest-sample-data) para banco de dados de líderes usando um dos vários métodos discutidos na [visão geral da ingestão](/azure/data-explorer/ingest-data-overview).

## <a name="attach-a-database"></a>Anexar um banco de dados

Existem vários métodos que você pode usar para anexar um banco de dados. Neste artigo, discutimos a anexação de um banco de dados usando C# ou um modelo do Azure Resource Manager. Para anexar um banco de dados, você deve ter permissões no cluster líder e no cluster de seguidores. Para obter mais informações sobre permissões, consulte [gerenciar permissões](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Anexar um banco de dados usando C #

#### <a name="needed-nugets"></a>NuGets necessário

* Instale [o Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Instale [microsoft.rest.clientRuntime.Azure.Autenticação para autenticação](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).

#### <a name="code-example"></a>Exemplo de código

```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-python"></a>Anexar um banco de dados usando Python

#### <a name="needed-modules"></a>Módulos necessários

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>Exemplo de código

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Anexar um banco de dados usando um modelo do Azure Resource Manager

Nesta seção, você aprende a anexar um banco de dados a um cluser existente usando um [modelo do Azure Resource Manager](../azure-resource-manager/management/overview.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "Union",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>Implantar o modelo 

Você pode implantar o modelo do Azure Resource Manager [usando o portal Azure](https://portal.azure.com) ou usando powershell.

   ![implantação de modelo](media/follower/template-deployment.png)


|**Configuração**  |**Descrição**  |
|---------|---------|
|Nome do cluster de seguidores     |  O nome do cluster de seguidores; onde o modelo será implantado.  |
|Nome das configurações do banco de dados anexado    |    O nome do objeto de configurações de banco de dados anexado. O nome pode ser qualquer string que seja única no nível do cluster.     |
|Nome do Banco de Dados     |      O nome do banco de dados a ser seguido. Se você quiser seguir todos os bancos de dados do líder, use '*'.   |
|ID de recurso de cluster líder    |   A id de recursos do grupo líder.      |
|Tipo de modificação de princípios padrão    |   O tipo de modificação principal padrão. Pode `Union` `Replace` ser, `None`ou . Para obter mais informações sobre o tipo de modificação principal padrão, consulte [o comando de controle do tipo de modificação principal](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Location   |   A localização de todos os recursos. O líder e o seguidor devem estar no mesmo local.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Verifique se o banco de dados foi anexado com sucesso

Para verificar se o banco de dados foi anexado com sucesso, encontre seus bancos de dados conectados no [portal Azure](https://portal.azure.com). 

1. Navegue até o cluster de seguidores e selecione **Bancos de Dados**
1. Procure por novos bancos de dados somente leitura na lista de banco de dados.

    ![Banco de dados de seguidores somente leitura](media/follower/read-only-follower-database.png)

Como alternativa:

1. Navegue até o cluster líder e selecione **Bancos de Dados**
2. Verifique se as bases de dados relevantes estão marcadas como **COMPARTILHADAS COM OUTROS** > **Sim**

    ![Ler e gravar bancos de dados anexados](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Desconecte o banco de dados de seguidores usando C # 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Desconecte o banco de dados de seguidores anexado do cluster de seguidores

O cluster de seguidores pode desprender qualquer banco de dados anexado da seguinte forma:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Desconecte o banco de dados de seguidores anexado do cluster líder

O cluster líder pode desvincular qualquer banco de dados anexado da seguinte forma:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>Desconecte o banco de dados de seguidores usando Python

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Desconecte o banco de dados de seguidores anexado do cluster de seguidores

O cluster de seguidores pode desprender qualquer banco de dados anexado da seguinte forma:

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Desconecte o banco de dados de seguidores anexado do cluster líder

O cluster líder pode desvincular qualquer banco de dados anexado da seguinte forma:

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Gerencie diretores, permissões e política de cache

### <a name="manage-principals"></a>Gerenciar diretores

Ao anexar um banco de dados, especifique o **tipo de modificação de "principais padrão"**. O padrão é manter a coleção de banco de dados líder de [diretores autorizados](/azure/kusto/management/access-control/index#authorization)

|**Tipo** |**Descrição**  |
|---------|---------|
|**Union**     |   Os principais bancos de dados anexados sempre incluirão os principais originais do banco de dados, além de novos diretores adicionais adicionados ao banco de dados de seguidores.      |
|**Substituir**   |    Nenhuma herança de diretores do banco de dados original. Novos diretores devem ser criados para o banco de dados anexado.     |
|**Nenhum**   |   Os principais bancos de dados anexados incluem apenas os principais do banco de dados original sem nenhum principal adicional.      |

Para obter mais informações sobre o uso de comandos de controle para configurar os principais autorizados, consulte [comandos de controle para gerenciar um cluster de seguidores](/azure/kusto/management/cluster-follower).

### <a name="manage-permissions"></a>Gerenciar permissões

O gerenciamento da permissão de banco de dados somente leitura é o mesmo que para todos os tipos de banco de dados. Consulte [as permissões de gerenciar no portal Azure](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Configurar a política de cache

O administrador do banco de dados de seguidores pode modificar a política de [cache](/azure/kusto/management/cache-policy) do banco de dados anexado ou qualquer uma de suas tabelas no cluster de hospedagem. O padrão é manter a coleção de banco de dados líder de políticas de cache de banco de dados e de nível de tabela. Você pode, por exemplo, ter uma política de cache de 30 dias no banco de dados do líder para executar relatórios mensais e uma política de cache de três dias no banco de dados de seguidores para consultar apenas os dados recentes para solução de problemas. Para obter mais informações sobre o uso de comandos de controle para configurar a política de cache no banco de dados ou tabela do seguidor, consulte [Comandos de Controle para gerenciar um cluster de seguidores](/azure/kusto/management/cluster-follower).

## <a name="limitations"></a>Limitações

* O seguidor e os grupos líderdevem estar na mesma região.
* [A ingestão por](/azure/data-explorer/ingest-data-streaming) streaming não pode ser usada em um banco de dados que está sendo seguido.
* A criptografia de dados usando [chaves gerenciadas pelo cliente](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault) não é suportada em clusters de líderes e seguidores. 
* Você não pode excluir um banco de dados que está conectado a um cluster diferente antes de desconectá-lo.
* Você não pode excluir um cluster que tenha um banco de dados conectado a um cluster diferente antes de desconectá-lo.
* Você não pode parar um cluster que tenha anexado o banco de dados de seguidores ou líderes. 

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre a configuração do cluster de seguidores, consulte [comandos de controle para gerenciar um cluster de seguidores](/azure/kusto/management/cluster-follower).
