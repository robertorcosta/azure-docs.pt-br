---
title: Configuração de DNS do ponto de extremidade privado do Azure
description: Aprenda a configuração de DNS do ponto de extremidade privado do Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 477a5ffa971120d1a98c09ac4ae8ebda1c82b770
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209019"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configuração de DNS do ponto de extremidade privado do Azure


Ao se conectar a um recurso de link privado usando um FQDN (nome de domínio totalmente qualificado) como parte da cadeia de conexão, é importante definir corretamente as configurações de DNS para resolver o endereço IP privado alocado. Os serviços do Azure existentes já podem ter uma configuração de DNS para usar ao se conectar por meio de um ponto de extremidade público. Isso precisa ser substituído para se conectar usando seu ponto de extremidade privado. 
 
O adaptador de rede associado ao ponto de extremidade privado contém o conjunto completo de informações necessárias para configurar o DNS, incluindo endereços IP privados e FQDN alocados para um determinado recurso de link privado. 
 
Você pode usar as seguintes opções para definir as configurações de DNS para pontos de extremidade privados: 
- **Use o arquivo de host (recomendado apenas para teste)**. Você pode usar o arquivo de host em uma máquina virtual para substituir o DNS.  
- **Use uma zona DNS privada**. Você pode usar [zonas DNS privadas](../dns/private-dns-privatednszone.md) para substituir a resolução DNS para um determinado ponto de extremidade particular. Uma zona DNS privada pode ser vinculada à sua rede virtual para resolver domínios específicos.
- **Use seu servidor DNS personalizado**. Você pode usar seu próprio servidor DNS para substituir a resolução DNS para um determinado recurso de link privado. Se o [servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) estiver hospedado em uma rede virtual, você poderá criar uma regra de encaminhamento de DNS para usar uma zona DNS privada para simplificar a configuração de todos os recursos de link privado.
 
> [!IMPORTANT]
> Não é recomendável substituir uma zona que esteja ativamente em uso para resolver pontos de extremidade públicos. As conexões com recursos não poderão ser resolvidas corretamente sem o encaminhamento de DNS para o DNS público. Para evitar problemas, crie um nome de domínio diferente ou siga o nome sugerido para cada serviço abaixo. 

## <a name="azure-services-dns-zone-configuration"></a>Configuração de zona de DNS dos serviços do Azure
Os serviços do Azure criarão um registro DNS de nome canônico (CNAME) no DNS público para redirecionar a resolução para os nomes de domínio privados sugeridos. Você poderá substituir a resolução pelo endereço IP privado dos seus pontos de extremidade privados. 
 
Seus aplicativos não precisam alterar a URL de conexão. Ao tentar resolver usando um DNS público, o servidor DNS agora será resolvido para seus pontos de extremidade privados. O processo não afeta seus aplicativos existentes. 

Para os serviços do Azure, use os nomes de zona recomendados, conforme descrito na tabela a seguir:

|Tipo de recurso de link privado   |Sub-recurso  |Nome da zona  |
|---------|---------|---------|
|Banco de BD SQL (Microsoft. SQL/Servers)    |  SQL Server (sqlServer)        |   privatelink.database.windows.net       |
|Análise de Synapse do Azure (Microsoft. SQL/Servers)    |  SQL Server (sqlServer)        | privatelink.database.windows.net |
|Conta de armazenamento (Microsoft. Storage/storageAccounts)    |  BLOB (BLOB, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Conta de armazenamento (Microsoft. Storage/storageAccounts)    |    Tabela (tabela, table_secondary)      |   privatelink.table.core.windows.net       |
|Conta de armazenamento (Microsoft. Storage/storageAccounts)    |    Fila (fila, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Conta de armazenamento (Microsoft. Storage/storageAccounts)   |    Arquivo (arquivo, file_secondary)      |    privatelink.file.core.windows.net      |
|Conta de armazenamento (Microsoft. Storage/storageAccounts)     |  Web (Web, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake sistema de arquivos Gen2 (Microsoft. Storage/storageAccounts)  |  Data Lake sistema de arquivos Gen2 (DFS, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|SQL    |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|MongoDB    |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Gremlin    |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Tabela|privatelink.table.cosmos.azure.com|
|Banco de dados do Azure para PostgreSQL-servidor único (Microsoft. DBforPostgreSQL/Servers)|postgresqlServer|privatelink.postgres.database.azure.com|
|Banco de dados do Azure para MySQL (Microsoft. DBforMySQL/Servers)|mysqlServer|privatelink.mysql.database.azure.com|
|Banco de dados do Azure para MariaDB (Microsoft. DBforMariaDB/Servers)|mariadbServer|privatelink.mariadb.database.azure.com|
|Azure Key Vault (Microsoft. keyvault/cofres)|cofre|privatelink.vaultcore.azure.net|
|Serviço kubernetes do Azure – API kubernetes (Microsoft. ContainerService/managedClusters)    | managedCluster | {GUID}. privatelink. {Region}. azmk8s. Io|
|Azure Search (Microsoft. Search/SearchServices)|searchService|privatelink.search.windows.net|   
|Registro de contêiner do Azure (Microsoft. ContainerRegistry/registros) | Registro | privatelink.azurecr.io |
|Configuração de Azure App (Microsoft. Appconfiguration/configurationStores)| configurationStore | privatelink.azconfig.io|
|Backup do Azure (Microsoft. Recoveryservices/cofres)| cofre |privatelink. {Region}. backup. WindowsAzure. com|
|Hub de eventos do Azure (Microsoft. EventHub/namespaces)| namespace |privatelink.servicebus.windows.net|
|Barramento de serviço do Azure (Microsoft. ServiceBus/namespaces) | namespace |privatelink.servicebus.windows.net|
|Retransmissão do Azure (Microsoft. Relay/namespaces) | namespace |privatelink.servicebus.windows.net|
|Grade de eventos do Azure (Microsoft. EventGrid/topics)     | topic | tópico. {Region}. privatelink. eventgrid. Azure. net|
|Grade de eventos do Azure (Microsoft. EventGrid/Domains) | domínio | controlador. {Region}. privatelink. eventgrid. Azure. net |
|Webapps do Azure (Microsoft. Web/sites)    | site | privatelink.azurewebsites.net |
|Azure Machine Learning (Microsoft. MachineLearningServices/Workspaces)    | workspace | privatelink.api.azureml.ms |
 


## <a name="dns-configuration-scenarios"></a>Cenários de configuração de DNS

O FQDN dos serviços resolve um endereço IP público, você precisa alterar a configuração do DNS para resolver o endereço IP privado do ponto de extremidade privado.

O DNS é um componente crítico para que o aplicativo funcione corretamente ao resolver de forma correta o endereço IP do ponto de extremidade privado.

Com base em suas preferências, os seguintes cenários estão disponíveis para a resolução de DNS integrada:

- [Cargas de trabalho de rede virtual sem servidor DNS personalizado](#virtual-network-workloads-without-custom-dns-server)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>Cargas de trabalho de rede virtual sem servidor DNS personalizado

Essa configuração é apropriada para cargas de trabalho de rede virtual sem servidor DNS personalizado. Nesse cenário, o cliente consulta o endereço IP do ponto de extremidade privado para o [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)DNS fornecido pelo Azure. O DNS do Azure será responsável pela resolução DNS das zonas DNS privadas.


 > [!NOTE]
> Este cenário está usando o banco de dados SQL do Azure recomendado DNS privado zona. Para outros serviços, você pode ajustar o modelo usando a seguinte [configuração de zona DNS dos serviços do Azure](#azure-services-dns-zone-configuration)de referência.

Para configurar corretamente, você precisará dos seguintes recursos:

- Rede virtual do cliente

- DNS privado zona [privatelink.Database.Windows.net](../dns/private-dns-privatednszone.md) com [um registro de tipo A](../dns/dns-zones-records.md#record-types)

- Informações particulares do ponto de extremidade (nome do registro FQDN e endereço IP privado)

O diagrama a seguir ilustra a sequência de resolução DNS de cargas de trabalho de rede virtual usando a zona DNS privada

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="rede virtual única e DNS fornecido pelo Azure":::

Esse modelo pode ser estendido para várias redes virtuais emparelhadas que estão associadas ao mesmo ponto de extremidade privado. Isso pode ser feito [adicionando novos links de rede virtual](../dns/private-dns-virtual-network-links.md) à zona DNS privada para todas as redes virtuais emparelhadas.

 > [!IMPORTANT]
>  Uma única zona DNS privada é necessária para essa configuração, a criação de várias zonas com o mesmo nome para diferentes redes virtuais precisaria de operações manuais para mesclar os registros DNS

Nesse cenário, há um [hub &](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) topologia de rede spoke com as redes spoke que compartilham um ponto de extremidade privado comum e todas as redes virtuais spoke estão vinculadas à mesma zona DNS privada. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub e spoke com o DNS fornecido pelo Azure":::


## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre pontos de extremidade privados](private-endpoint-overview.md)
