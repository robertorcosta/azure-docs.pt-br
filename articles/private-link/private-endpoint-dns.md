---
title: Configuração de DNS do ponto de extremidade privado do Azure
description: Aprenda a configuração de DNS do ponto de extremidade privado do Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: 6e3d87d613db63e05ddee47d43aead779eca75c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628002"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configuração de DNS do ponto de extremidade privado do Azure


Quando você estiver se conectando a um recurso de link privado usando um FQDN (nome de domínio totalmente qualificado) como parte da cadeia de conexão, é importante definir corretamente as configurações de DNS para resolver o endereço IP privado alocado. Os serviços de Microsoft Azure existentes já podem ter uma configuração de DNS para usar ao se conectar por meio de um ponto de extremidade público. Essa configuração precisa ser substituída para se conectar usando seu ponto de extremidade privado. 
 
O adaptador de rede associado ao ponto de extremidade privado contém o conjunto completo de informações necessárias para configurar o DNS, incluindo endereços IP privados e FQDN alocados para um recurso de link particular específico. 
 
Você pode usar as seguintes opções para definir as configurações de DNS para pontos de extremidade privados: 
- **Use o arquivo de host (recomendado apenas para teste)**. Você pode usar o arquivo de host em uma máquina virtual para substituir o DNS.  
- **Use a zona DNS privada**. Você pode usar [zonas DNS privadas](../dns/private-dns-privatednszone.md) para substituir a resolução DNS para um ponto de extremidade particular específico. Uma zona DNS privada pode ser vinculada à sua rede virtual para resolver domínios específicos.
- **Use o encaminhador DNS (opcional)**. Você pode usar o encaminhador DNS para substituir a resolução DNS para um recurso de link particular específico. Se o [servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) estiver hospedado em uma rede virtual, você poderá criar uma regra de encaminhamento de DNS para usar uma zona DNS privada para simplificar a configuração para todos os recursos de link privado.
 
> [!IMPORTANT]
> Não é recomendável substituir uma zona que está ativamente em uso para resolver pontos de extremidade públicos. As conexões com recursos não poderão ser resolvidas corretamente sem o encaminhamento de DNS para o DNS público. Para evitar problemas, crie um nome de domínio diferente ou siga o nome sugerido para cada serviço abaixo. 

## <a name="azure-services-dns-zone-configuration"></a>Configuração de zona de DNS dos serviços do Azure
Os serviços do Azure criarão um registro DNS de nome canônico (CNAME) no serviço DNS público para redirecionar a resolução para o nome de domínio privado sugerido. Você pode substituir a resolução pelo endereço IP privado dos seus pontos de extremidade privados. 
 
Seus aplicativos não precisam alterar a URL de conexão. Ao tentar resolver usando um serviço DNS público, o servidor DNS agora será resolvido para seus pontos de extremidade privados. O processo não afeta seus aplicativos existentes. 

> [!IMPORTANT]
> Redes privadas que já usam a zona DNS privada para um determinado tipo só poderão se conectar a recursos públicos se não tiverem conexões de ponto de extremidade particulares, caso contrário, uma configuração de DNS correspondente será necessária na zona DNS privada para concluir a sequência de resolução DNS. 

Para os serviços do Azure, use os nomes de zona recomendados, conforme descrito na tabela a seguir:

| Tipo/subrecurso de recurso de link privado |Nome da zona de DNS privado | Encaminhadores de zona DNS pública |
|---|---|---|---|
| Automação do Azure/(Microsoft. Automation/automationAccounts)/webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Banco de dados SQL do Azure (Microsoft. SQL/Servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Análise de Synapse do Azure (Microsoft. SQL/Servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Conta de armazenamento (Microsoft. Storage/storageAccounts)/BLOB (BLOB, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Conta de armazenamento (Microsoft. Storage/storageAccounts)/tabela (tabela, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Conta de armazenamento (Microsoft. Storage/storageAccounts)/fila (fila, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Conta de armazenamento (Microsoft. Storage/storageAccounts)/arquivo (arquivo file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Conta de armazenamento (Microsoft. Storage/storageAccounts)/Web (Web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Azure Data Lake sistema de arquivos Gen2 (Microsoft. Storage/storageAccounts)/Data Lake do sistema de arquivos Gen2 (DFS dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Tabela | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Banco de Dados do Azure para PostgreSQL - servidor único (Microsoft.DBforPostgreSQL/servers)/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Banco de Dados do Azure para MySQL (Microsoft.DBforMySQL/servers)/mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Banco de Dados do Azure para MariaDB (Microsoft.DBforMariaDB/servers)/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft.KeyVault/vaults)/vault | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Serviço kubernetes do Azure – API kubernetes (Microsoft. ContainerService/managedClusters)/Management | privatelink.{region}.azmk8s.io | {region}.azmk8s.io |
| Azure Search (Microsoft.Search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Registro de Contêiner do Azure (Microsoft.ContainerRegistry/registries)/registry | privatelink.azurecr.io | azurecr.io |
| Configuração de Aplicativos do Azure (Microsoft.AppConfiguration/configurationStores)/configurationStore | privatelink.azconfig.io | azconfig.io |
| Backup do Azure (Microsoft.RecoveryServices/vaults)/vault | privatelink.{region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Hubs de eventos do Azure (Microsoft. EventHub/namespaces)/namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Barramento de Serviço do Azure (Microsoft.ServiceBus/namespaces)/namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Hub IoT do Azure (Microsoft. Devices/IotHubs)/iotHub | privatelink.azure-devices.net | azure-devices.net |
| Retransmissão do Azure (Microsoft.Relay/namespaces)/namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Grade de Eventos do Azure (Microsoft.EventGrid/topics)/tópico | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Grade de Eventos do Azure (Microsoft.EventGrid/domains)/domínio | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Aplicativos Web do Azure (Microsoft. Web/sites)/sites | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft. MachineLearningServices/Workspaces)/espaço de trabalho | privatelink.api.azureml.ms | api.azureml.ms |
| Hub IoT (Microsoft. Devices/IotHubs)/IotHub | privatelink.azure-devices.net | azure-devices.net |
| Signalr (Microsoft. SignalRService/Signalr)/signalr | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (Microsoft. insights/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| Serviços cognitivas (Microsoft. Cognitivaservices/contas)/conta | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Sincronização de Arquivos do Azure (Microsoft. StorageSync/storageSyncServices)/AFS |  privatelink.afs.azure.net  |  afs.azure.net  |

 
## <a name="dns-configuration-scenarios"></a>Cenários de configuração de DNS

O FQDN dos serviços é resolvido automaticamente para um endereço IP público. Para resolver o endereço IP privado do ponto de extremidade privado, você deve alterar sua configuração de DNS adequadamente.

O DNS é um componente crítico para fazer com que o aplicativo funcione corretamente resolvendo com êxito o endereço IP do ponto de extremidade privado.

Com base em suas preferências, os seguintes cenários estão disponíveis com a resolução de DNS integrada:

- [Cargas de trabalho de rede virtual sem servidor DNS personalizado](#virtual-network-workloads-without-custom-dns-server)
- [Cargas de trabalho locais usando um encaminhador de DNS](#on-premises-workloads-using-a-dns-forwarder)
- [Rede virtual e cargas de trabalho locais usando um encaminhador DNS](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>Cargas de trabalho de rede virtual sem servidor DNS personalizado

Essa configuração é apropriada para cargas de trabalho de rede virtual sem um servidor DNS personalizado. Nesse cenário, o cliente consulta o endereço IP do ponto de extremidade privado para o serviço DNS fornecido pelo Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). O DNS do Azure será responsável pela resolução DNS das zonas DNS privadas.

> [!NOTE]
> Esse cenário usa a zona DNS privada recomendada do banco de dados SQL do Azure. Para outros serviços, você pode ajustar o modelo usando a seguinte referência: [configuração de zona de DNS dos serviços do Azure](#azure-services-dns-zone-configuration).

Para configurar corretamente, você precisa dos seguintes recursos:

- Rede virtual de cliente

- DNS privado zona [privatelink.Database.Windows.net](../dns/private-dns-privatednszone.md)  com [um registro de tipo A](../dns/dns-zones-records.md#record-types)

- Informações particulares do ponto de extremidade (nome do registro FQDN e endereço IP privado)

A captura de tela a seguir ilustra a sequência de resolução DNS de cargas de trabalho de rede virtual usando a zona DNS privada:

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Rede virtual única e DNS fornecido pelo Azure":::

Esse modelo pode ser estendido para várias redes virtuais emparelhadas que estão associadas ao mesmo ponto de extremidade privado. Isso pode ser feito [adicionando novos links de rede virtual](../dns/private-dns-virtual-network-links.md) à zona DNS privada para todas as redes virtuais emparelhadas.

> [!IMPORTANT]
> Uma única zona DNS privada é necessária para essa configuração. A criação de várias zonas com o mesmo nome para diferentes redes virtuais precisaria de operações manuais para mesclar os registros DNS.

> [!IMPORTANT]
> Se você estiver usando um ponto de extremidade privado em um modelo de Hub e spoke de uma assinatura diferente, reutilize a mesma zona DNS privada no Hub.

Nesse cenário, há uma topologia de rede [Hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) com as redes spoke que compartilham um ponto de extremidade privado comum e todas as redes virtuais spoke estão vinculadas à mesma zona DNS privada. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Rede virtual única e DNS fornecido pelo Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Cargas de trabalho locais usando um encaminhador de DNS

Para cargas de trabalho locais para resolver um FQDN de um ponto de extremidade privado para o endereço IP privado, você deve usar um encaminhador DNS para implantar a resolução da [zona DNS pública](#azure-services-dns-zone-configuration) do serviço do Azure no Azure.

O cenário a seguir é apropriado para uma rede local que tenha um encaminhador DNS no Azure, que por sua vez é responsável por resolver todas as consultas DNS por meio de um encaminhador de nível de servidor para o [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)DNS fornecido pelo Azure. 

> [!NOTE]
> Esse cenário usa a zona DNS privada recomendada do banco de dados SQL do Azure.Para outros serviços, você pode ajustar o modelo usando a seguinte referência: [configuração de zona de DNS dos serviços do Azure](#azure-services-dns-zone-configuration).

Para configurar corretamente, você precisa dos seguintes recursos:

- Rede local
- Rede virtual [conectada a redes locais](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Encaminhador de DNS implantado no Azure 
- Zonas de DNS privado [privatelink.Database.Windows.net](../dns/private-dns-privatednszone.md)   com [um registro de tipo A](../dns/dns-zones-records.md#record-types)
- Informações particulares do ponto de extremidade (nome do registro FQDN e endereço IP privado)

O diagrama a seguir ilustra a sequência de resolução DNS de uma rede local que usa um encaminhador DNS implantado no Azure, onde a resolução é feita por uma zona DNS privada [vinculada a uma rede virtual](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Rede virtual única e DNS fornecido pelo Azure":::

Essa configuração pode ser estendida para uma rede local que já tenha uma solução de DNS em vigor. 
A solução DNS local precisa ser configurada para encaminhar o tráfego DNS para o DNS do Azure por meio de um [encaminhador condicional](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) que faz referência ao ENCAMINHAdor DNS implantado no Azure.

> [!NOTE]
> Esse cenário usa a zona DNS privada recomendada do banco de dados SQL do Azure. Para outros serviços, você pode ajustar o modelo usando a seguinte referência: [configuração de zona de DNS dos serviços do Azure](#azure-services-dns-zone-configuration)

Para configurar corretamente, você precisa dos seguintes recursos:

- Rede local com uma solução de DNS personalizada em vigor 
- Rede virtual [conectada a redes locais](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Encaminhador de DNS implantado no Azure
- Zonas de DNS privado [privatelink.Database.Windows.net](../dns/private-dns-privatednszone.md)    com [um registro de tipo A](../dns/dns-zones-records.md#record-types)
- Informações particulares do ponto de extremidade (nome do registro FQDN e endereço IP privado)

O diagrama a seguir ilustra a sequência de resolução DNS de uma rede local que encaminha condicionalmente o tráfego DNS para o Azure, onde a resolução é feita por uma zona DNS privada [vinculada a uma rede virtual](../dns/private-dns-virtual-network-links.md).

> [!IMPORTANT]
> O encaminhamento condicional deve ser feito para o [encaminhador de zona DNS pública](#azure-services-dns-zone-configuration)recomendado.Por exemplo:  `database.windows.net`   em vez de **privatelink**. Database.Windows.net.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Rede virtual única e DNS fornecido pelo Azure":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Rede virtual e cargas de trabalho locais usando um encaminhador DNS

Para obter uma abordagem geral adequada para cargas de trabalho que precisam de acesso a um ponto de extremidade privado de redes virtuais e locais, você deve usar um encaminhador DNS compartilhado para fazer a resolução da [zona DNS pública](#azure-services-dns-zone-configuration) do serviço do Azure implantada no Azure.

O cenário a seguir é apropriado para uma rede local que tenha um encaminhador DNS no Azure, e redes virtuais que precisam de acesso ao ponto de extremidade privado localizado em uma rede de Hub compartilhada.  

Esse encaminhador DNS é responsável por resolver todas as consultas DNS por meio de um encaminhador de nível de servidor para o serviço DNS fornecido pelo Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md).

> [!IMPORTANT]
> Uma única zona DNS privada é necessária para essa configuração. Todas as conexões de cliente feitas de [redes virtuais emparelhadas](../virtual-network/virtual-network-peering-overview.md) e locais também devem usar a mesma zona DNS privada.

> [!NOTE]
> Esse cenário usa a zona DNS privada recomendada do banco de dados SQL do Azure. Para outros serviços, você pode ajustar o modelo usando a seguinte referência: [configuração de zona de DNS dos serviços do Azure](#azure-services-dns-zone-configuration).

Para configurar corretamente, você precisa dos seguintes recursos:

- Rede local
- Rede virtual [conectada a redes locais](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- [Rede virtual emparelhada](../virtual-network/virtual-network-peering-overview.md) 
- Encaminhador de DNS implantado no Azure
- Zonas de DNS privado [privatelink.Database.Windows.net](../dns/private-dns-privatednszone.md)    com [um registro de tipo A](../dns/dns-zones-records.md#record-types)
- Informações particulares do ponto de extremidade (nome do registro FQDN e endereço IP privado)

O diagrama a seguir ilustra a sequência de resolução DNS de uma rede local e virtual que usa um encaminhador DNS implantado no Azure, onde a resolução é feita por uma zona DNS privada [vinculada a uma rede virtual](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Rede virtual única e DNS fornecido pelo Azure":::

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre pontos de extremidade privados](private-endpoint-overview.md)
