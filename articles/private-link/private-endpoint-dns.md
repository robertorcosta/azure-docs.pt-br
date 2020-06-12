---
title: Configuração DNS do ponto de extremidade privado do Azure
description: Conheça a configuração DNS do ponto de extremidade privado do Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 7c8ff0808ada522dc24ef3c27156f4151832fbcd
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715929"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configuração DNS do ponto de extremidade privado do Azure


Ao se conectar a um recurso de link privado usando um FQDN (nome de domínio totalmente qualificado) como parte da cadeia de conexão, é importante definir corretamente as configurações de DNS para resolver o endereço IP privado alocado. Os serviços do Azure existentes já podem ter uma configuração de DNS para usar ao se conectar por meio de um ponto de extremidade público. Essa configuração precisa ser substituída para se conectar usando seu ponto de extremidade privado. 
 
O adaptador de rede associado ao ponto de extremidade privado contém o conjunto completo de informações necessárias para configurar o DNS, incluindo endereços IP privados e FQDN alocados para um determinado recurso de link privado. 
 
Você pode usar as seguintes opções para definir as configurações de DNS para pontos de extremidade privados: 
- **Use o arquivo de host (apenas recomendado para testes)** . Você pode usar o arquivo de host em uma máquina virtual para substituir o DNS.  
- **Use a zona DNS privada**. Você pode usar [zonas DNS privadas](../dns/private-dns-privatednszone.md) para substituir a resolução DNS para um determinado ponto de extremidade privado. Uma zona DNS privada pode ser vinculada à sua rede virtual para resolver domínios específicos.
- **Use o encaminhador de DNS (opcional)** . Você pode usar o encaminhador de DNS para substituir a resolução DNS para um determinado recurso de link privado. Se o [servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) estiver hospedado em uma rede virtual, você poderá criar uma regra de encaminhamento de DNS para usar uma zona DNS privada para simplificar a configuração para todos os recursos de link privado.
 
> [!IMPORTANT]
> Não é recomendável substituir uma zona que esteja ativamente em uso para resolver pontos de extremidade públicos. As conexões com recursos não poderão ser resolvidas corretamente sem o encaminhamento de DNS para o DNS público. Para evitar problemas, crie um nome de domínio diferente ou siga o nome sugerido para cada serviço abaixo. 

## <a name="azure-services-dns-zone-configuration"></a>Configuração de zona de DNS dos serviços do Azure
Os serviços do Azure criarão um registro DNS de nome canônico (CNAME) no DNS público para redirecionar a resolução para o nome de domínio privado sugerido. Você pode substituir a resolução pelo endereço IP privado dos seus pontos de extremidade privados. 
 
Seus aplicativos não precisam alterar a URL de conexão. Ao tentar resolver usando um DNS público, o servidor DNS agora será resolvido para seus pontos de extremidade privados. O processo não afeta seus aplicativos existentes. 

Para os serviços do Azure, use os nomes de zona recomendados, conforme descrito na tabela a seguir:

| Tipo de recurso/sub-recurso de link privado |Nome da zona DNS privada | Encaminhadores de zona DNS pública |
|---|---|---|---|
| BD SQL (Microsoft.Sql/servers)/Sql Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft.Sql/servers)/Sql Server  | privatelink.database.windows.net | database.windows.net |
| Conta de armazenamento (Microsoft.Storage/storageAccounts)/Blob (blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Conta de armazenamento (Microsoft.Storage/storageAccounts)/Tabela (table, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Conta de armazenamento (Microsoft.Storage/storageAccounts)/Fila (queue, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Conta de armazenamento (Microsoft.Storage/storageAccounts)/Arquivo (file, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Conta de armazenamento (Microsoft.Storage/storageAccounts)/Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Data Lake File System Gen2 (Microsoft.Storage/storageAccounts)/Data Lake File System Gen2 (dfs, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Tabela | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Banco de Dados do Azure para PostgreSQL - servidor único (Microsoft.DBforPostgreSQL/servers)/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Banco de Dados do Azure para MySQL (Microsoft.DBforMySQL/servers)/mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Banco de Dados do Azure para MariaDB (Microsoft.DBforMariaDB/servers)/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft.KeyVault/vaults)/vault | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Serviço de Kubernetes do Azure - API de Kubernetes (Microsoft.ContainerService/managedClusters)/managedCluster | privatelink.{region}.azmk8s.io | {region}.azmk8s.io |
| Azure Search (Microsoft.Search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Registro de Contêiner do Azure (Microsoft.ContainerRegistry/registries)/registry | privatelink.azurecr.io | azurecr.io |
| Configuração de Aplicativos do Azure (Microsoft.AppConfiguration/configurationStores)/configurationStore | privatelink.azconfig.io | azconfig.io |
| Backup do Azure (Microsoft.RecoveryServices/vaults)/vault | privatelink.{region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Hub de eventos do Azure (Microsoft.EventHub/namespaces)/namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Barramento de Serviço do Azure (Microsoft.ServiceBus/namespaces)/namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Retransmissão do Azure (Microsoft.Relay/namespaces)/namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Grade de Eventos do Azure (Microsoft.EventGrid/topics)/tópico | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Grade de Eventos do Azure (Microsoft.EventGrid/domains)/domínio | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| WebApps do Azure (Microsoft.Web/sites)/site | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning(Microsoft.MachineLearningServices/workspaces)/espaço de trabalho | privatelink.api.azureml.ms | api.azureml.ms |

 


## <a name="dns-configuration-scenarios"></a>Cenários de configuração de DNS

O FQDN dos serviços é resolvido automaticamente para um endereço IP público; portanto, para resolver o endereço IP privado do ponto de extremidade privado, você deve alterar sua configuração de DNS adequadamente.

O DNS é um componente crítico para que o aplicativo funcione corretamente ao resolver de forma correta o endereço IP do ponto de extremidade privado.

Com base em suas preferências, os seguintes cenários estão disponíveis para a resolução de DNS integrada:

- [Cargas de trabalho de rede virtual sem servidor DNS personalizado](#virtual-network-workloads-without-custom-dns-server)
- [Cargas de trabalho locais usando um encaminhador de DNS](#on-premises-workloads-using-a-dns-forwarder)

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Cargas de trabalho de rede virtual sem servidor DNS personalizado

Essa configuração é apropriada para cargas de trabalho de rede virtual sem servidor DNS personalizado. Nesse cenário, o cliente consulta o endereço IP do ponto de extremidade privado para o DNS fornecido pelo Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). O DNS do Azure será responsável pela resolução DNS das zonas DNS privadas.


> [!NOTE]
> Este cenário está usando a zona DNS privada recomendada do banco de dados SQL do Azure. Para outros serviços, você pode ajustar o modelo usando a seguinte referência [Configuração de zona DNS dos serviços do Azure](#azure-services-dns-zone-configuration).

Para configurar corretamente, você precisará dos seguintes recursos:

- Rede virtual de cliente

- Zona DNS privada [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) com [registro de tipo A](../dns/dns-zones-records.md#record-types)

- Informações do ponto de extremidade privado (nome do registro FQDN e endereço IP privado)

O diagrama a seguir ilustra a sequência de resolução DNS de cargas de trabalho de rede virtual usando a zona DNS privada

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Rede virtual única e DNS fornecido pelo Azure":::

Esse modelo pode ser estendido para várias redes virtuais emparelhadas que estão associadas ao mesmo ponto de extremidade privado. Isso pode ser feito [adicionando novos links de rede virtual](../dns/private-dns-virtual-network-links.md) à zona DNS privada para todas as redes virtuais emparelhadas.

> [!IMPORTANT]
> Uma única zona DNS privada é necessária para essa configuração, criar várias zonas com o mesmo nome para diferentes redes virtuais precisaria de operações manuais para mesclar os registros DNS

Nesse cenário, há uma topologia de rede [hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) com as redes spoke que compartilham um ponto de extremidade privado comum e todas as redes virtuais spoke estão vinculadas à mesma zona DNS privada. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub e spoke com DNS fornecido pelo Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Cargas de trabalho locais usando um encaminhador de DNS

Para que as cargas de trabalho locais possam resolver um FQDN de um ponto de extremidade privado para o endereço IP privado, você deverá usar um encaminhador de DNS para fazer a resolução da [zona DNS pública](#azure-services-dns-zone-configuration) do serviço do Azure implantada no Azure.


O cenário a seguir é apropriado para uma rede local que tenha um encaminhador DNS no Azure, que, por sua vez, é responsável por resolver todas as consultas DNS por meio de um encaminhador de nível de servidor para o DNS fornecido pelo Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) 

> [!NOTE]
> Este cenário está usando a zona DNS privada recomendada do banco de dados SQL do Azure. Para outros serviços, você pode ajustar o modelo usando a seguinte referência  [Configuração de zona DNS dos serviços do Azure](#azure-services-dns-zone-configuration).

Para configurar corretamente, você precisará dos seguintes recursos:

- Rede local
- Rede virtual [conectada a redes locais](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Encaminhador de DNS implantado no Azure 
- Zona DNS privada  [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) com [registro de tipo A](../dns/dns-zones-records.md#record-types)
- Informações do ponto de extremidade privado (nome do registro FQDN e endereço IP privado)

O diagrama a seguir ilustra a sequência de resolução DNS de uma rede local que usa um encaminhador de DNS implantado no Azure, onde a resolução é feita por uma zona DNS privada [vinculada a uma rede virtual.](../dns/private-dns-virtual-network-links.md)

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Local usando o DNS do Azure":::

Essa configuração pode ser estendida para uma rede local que já tenha uma solução de DNS em vigor. 
A solução DNS local precisa ser configurada para encaminhar o tráfego DNS para o DNS do Azure por meio de um  [encaminhador condicional](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) referenciando o encaminhador de DNS implantado no Azure.

> [!NOTE]
> Este cenário está usando a zona DNS privada recomendada do banco de dados SQL do Azure. Para outros serviços, você pode ajustar o modelo usando a seguinte referência  [Configuração de zona DNS dos serviços do Azure](#azure-services-dns-zone-configuration).

Para configurar corretamente, você precisará dos seguintes recursos:

- Rede local com uma solução de DNS personalizada em vigor 
- Rede virtual [conectada a redes locais](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Encaminhador de DNS implantado no Azure
- Zona DNS privada  [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)  com [registro de tipo A](../dns/dns-zones-records.md#record-types)
- Informações do ponto de extremidade privado (nome do registro FQDN e endereço IP privado)

O diagrama a seguir ilustra a sequência de resolução DNS de uma rede local que encaminha condicionalmente o tráfego de DNS para o Azure, onde a resolução é feita por uma zona DNS privada  [vinculada a uma rede virtual.](../dns/private-dns-virtual-network-links.md)

> [!IMPORTANT]
> O encaminhamento condicional deve ser feito para o [**encaminhador de zona DNS pública recomendado**](#azure-services-dns-zone-configuration) Ex: `database.windows.net` , em vez de **privatelink**.database.windows.net

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Encaminhamento local para o DNS do Azure":::


## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre ponto de extremidade privado](private-endpoint-overview.md)
