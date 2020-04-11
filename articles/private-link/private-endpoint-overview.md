---
title: O que é um Ponto Final Privado do Azure?
description: Saiba mais sobre o Azure Private Endpoint
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: d10b6c52310da3d799a7fe78c83284960318f82e
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115241"
---
# <a name="what-is-azure-private-endpoint"></a>O que é o Azure Private Endpoint?

O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura a um serviço com tecnologia do Link Privado do Azure. O Private Endpoint usa um endereço IP privado do seu VNet, efetivamente trazendo o serviço para o seu VNet. O serviço pode ser um serviço Azure, como Azure Storage, Azure Cosmos DB, SQL, etc. ou seu próprio [Serviço de Link Privado](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Propriedades privadas do endpoint 
 Um ponto final privado especifica as seguintes propriedades: 


|Propriedade  |Descrição |
|---------|---------|
|Nome    |    Um nome único dentro do grupo de recursos.      |
|Sub-rede    |  A sub-rede para implantar e alocar endereços IP privados a partir de uma rede virtual. Para requisitos de sub-rede, consulte a seção Limitações neste artigo.         |
|Recurso de link privado    |   O recurso de link privado para se conectar usando ID de recurso ou alias, a partir da lista de tipos disponíveis. Um identificador de rede exclusivo será gerado para todo o tráfego enviado a este recurso.       |
|Subrecursos de destino   |      O subrecurso para conectar. Cada tipo de recurso de link privado tem diferentes opções para selecionar com base na preferência.    |
|Método de aprovação de conexão    |  Automático ou manual. Com base em permissões RBAC (Role-based Access Control, controle de acesso baseado em função), seu ponto final privado pode ser aprovado automaticamente. Se você tentar se conectar a um recurso de link privado sem RBAC, use o método manual para permitir que o proprietário do recurso aprove a conexão.        |
|Mensagem de Solicitação     |  Você pode especificar uma mensagem para que as conexões solicitadas sejam aprovadas manualmente. Esta mensagem pode ser usada para identificar uma solicitação específica.        |
|Status da conexão   |   Uma propriedade somente leitura que especifica se o ponto final privado está ativo. Apenas pontos finais privados em um estado aprovado podem ser usados para enviar tráfego. Estados adicionais disponíveis: <br>-**Aprovado**: A conexão foi aprovada automaticamente ou manualmente e está pronta para ser usada.</br><br>-**Pendente**: A conexão foi criada manualmente e está pendente de aprovação pelo proprietário do recurso de link privado.</br><br>-**Rejeitado**: A conexão foi rejeitada pelo proprietário do recurso de link privado.</br><br>-**Desconectado**: A conexão foi removida pelo proprietário do recurso de link privado. O ponto final privado torna-se informativo e deve ser excluído para limpeza. </br>|

Aqui estão alguns detalhes-chave sobre pontos finais privados: 
- O ponto final privado permite a conectividade entre os consumidores do mesmo VNet, VNets regionalmente peered, VNets de peered globalmente e em instalações usando [VPN](https://azure.microsoft.com/services/vpn-gateway/) ou [Express Route](https://azure.microsoft.com/services/expressroute/) e serviços alimentados pelo Private Link.
 
- Ao criar um ponto final privado, uma interface de rede também é criada para o ciclo de vida do recurso. A interface é atribuída a um endereço IP privado da sub-rede que mapeia para o Serviço de Link Privado.
 
- O ponto final privado deve ser implantado na mesma região que a rede virtual. 
 
- O recurso de link privado pode ser implantado em uma região diferente da rede virtual e do ponto final privado.
 
- Vários pontos finais privados podem ser criados usando o mesmo recurso de link privado. Para uma única rede usando uma configuração comum de servidor DNS, a prática recomendada é usar um único ponto final privado para um determinado recurso de link privado para evitar entradas duplicadas ou conflitos na resolução de DNS. 
 
- Vários pontos finais privados podem ser criados nas mesmas ou diferentes sub-redes dentro da mesma rede virtual. Há limites para o número de pontos finais privados que você pode criar em uma assinatura. Para obter detalhes, consulte [os limites do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


 
## <a name="private-link-resource"></a>Recurso de link privado 
Um recurso de link privado é o alvo de destino de um determinado ponto final privado. A seguir está uma lista de tipos de recursos de link privado disponíveis: 
 
|Nome do recurso de link privado  |Tipo de recurso   |Sub-recursos  |
|---------|---------|---------|
|**Serviço de link privado** (seu próprio serviço)   |  Microsoft.Network/privateLinkServices       | empty |
|**Banco de dados SQL do Azure** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        | 
|**Armazenamento do Azure**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Mesa (tabela, table_secondary)<BR> Fila (fila, queue_secondary)<BR> Arquivo (arquivo, file_secondary)<BR> Web (web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Data Lake File System Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/banco de dadosContas | Sql, MongoDB, Cassandra, Gremlin, Mesa|
|**Banco de dados Azure para postgreSQL -Único servidor** | Microsoft.DBforPostgreSQL/servers   | postgresqlServer |
|**Banco de Dados do Azure para MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Banco de Dados do Azure para MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Cofre de Chaves Azure** | Microsoft.KeyVault/vaults    | cofre |
|**Serviço Azure Kubernetes - Kubernetes API** | Microsoft.ContainerService/managedClusters | managedCluster |
|**Busca do Azure** | Microsoft.Search/searchService| Searchservice|  
|**Registro de Contêineres Azure** | Microsoft.ContainerRegistry/registries  | Registro |
|**Configuração de Aplicativo do Azure** | Microsoft.Appconfiguration/configurationStores   | configuraçãoArmazenar |
|**Serviço de Backup do Azure** | Microsoft.RecoveryServices/vaults   | cofre |
|**Hub de eventos azure** | Microsoft.EventHub/namespaces    | namespace |
|**Barramento de Serviço do Azure** | Microsoft.ServiceBus/namespaces | namespace |
|**Retransmissão do Azure** | Microsoft.Relay/namespaces | namespace |
|**Grade de Eventos Azure** | Microsoft.EventGrid/topics  | topic |
|**Grade de Eventos Azure** | Microsoft.EventGrid/domínios | domínio |
|**Azure WebApps** | Microsoft.Web/sites    | site |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces  | workspace |
  
 
## <a name="network-security-of-private-endpoints"></a>Segurança de rede de pontos finais privados 
Ao usar pontos finais privados para serviços do Azure, o tráfego é protegido para um recurso de link privado específico. A plataforma executa um controle de acesso para validar conexões de rede que alcançam apenas o recurso de link privado especificado. Para acessar recursos adicionais dentro do mesmo serviço Do Azure, são necessários pontos finais privados adicionais. 
 
Você pode bloquear completamente suas cargas de trabalho de acessar pontos finais públicos para se conectar a um serviço Azure suportado. Esse controle fornece uma camada adicional de segurança de rede aos seus recursos, fornecendo uma proteção de exfiltração incorporada que impede o acesso a outros recursos hospedados no mesmo serviço Do Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Acesso a um recurso de link privado usando fluxo de trabalho de aprovação 
Você pode se conectar a um recurso de link privado usando os seguintes métodos de aprovação de conexão:
- **Aprovado automaticamente** quando você possui ou tem permissão no recurso de link privado específico. A permissão necessária é baseada no tipo de recurso de link privado no seguinte formato: Microsoft. \<do> provedor/<resource_type>/privateEndpointConexãoAprovação/ação
- **Solicitação manual** quando você não tem a permissão necessária e gostaria de solicitar acesso. Um fluxo de trabalho de aprovação será iniciado. O ponto de extremidade privado e a conexão do ponto de extremidade particular subsequente serão criados em um estado "Pendente". O proprietário do recurso de link privado é responsável por aprovar a conexão. Depois de aprovado, o ponto final privado é habilitado para enviar tráfego normalmente, como mostrado no diagrama de fluxo de trabalho de aprovação a seguir.  

![aprovação do fluxo de trabalho](media/private-endpoint-overview/private-link-paas-workflow.png)
 
O proprietário de recursos de link privado pode executar as seguintes ações em uma conexão de ponto final privado: 
- Revise todos os detalhes das conexões de ponto final privados. 
- Aprove uma conexão de ponto final privado. O ponto final privado correspondente será habilitado para enviar tráfego para o recurso de link privado. 
- Rejeite uma conexão de ponto final privado. O ponto final privado correspondente será atualizado para refletir o status.
- Exclua uma conexão de ponto final privado em qualquer estado. O ponto final privado correspondente será atualizado com um estado desconectado para refletir a ação, o proprietário do ponto final privado só poderá excluir o recurso neste momento. 
 
> [!NOTE]
> Apenas um ponto final privado em um estado aprovado pode enviar tráfego para um determinado recurso de link privado. 

### <a name="connecting-using-alias"></a>Conectando usando alias
Alias é um apelido único que é gerado quando o proprietário do serviço cria o serviço de link privado atrás de um balanceador de carga padrão. O proprietário do serviço pode compartilhar este Alias com seus consumidores offline. Os consumidores podem solicitar uma conexão ao serviço de link privado usando o RECURSO URI ou o Alias. Se você quiser se conectar usando alias, você deve criar um ponto final privado usando o método de aprovação manual de conexão. Para usar o método de aprovação manual de conexão, defina o parâmetro de solicitação manual para true durante o fluxo de criação de ponto final privado. Veja [o new-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) e [a rede az private-endpoint criar](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) para obter detalhes. 

## <a name="dns-configuration"></a>Configuração de DNS 
Ao se conectar a um recurso de link privado usando um nome de domínio totalmente qualificado (FQDN) como parte da seqüência de conexões, é importante configurar corretamente as configurações de DNS para resolver para o endereço IP privado alocado. Os serviços existentes do Azure já podem ter uma configuração de DNS para usar ao se conectar em um ponto final público. Isso precisa ser substituído para se conectar usando seu ponto final privado. 
 
A interface de rede associada ao ponto final privado contém o conjunto completo de informações necessárias para configurar seu DNS, incluindo endereços IP fqdn e privados alocados para um determinado recurso de link privado. 
 
Você pode usar as seguintes opções para configurar as configurações do DNS para pontos finais privados: 
- **Use o arquivo Host (recomendado apenas para testes)**. Você pode usar o arquivo host em uma máquina virtual para substituir o DNS.  
- **Use uma zona dns privada**. Você pode usar zonas DNS privadas para substituir a resolução DNS para um determinado ponto final privado. Uma região de DNS privada pode ser vinculada à sua rede virtual para resolver domínios específicos.
- **Use seu servidor DNS personalizado.** Você pode usar seu próprio servidor DNS para substituir a resolução DNS para um determinado recurso de link privado. Se o [servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) estiver hospedado em uma rede virtual, você poderá criar uma regra de encaminhamento de DNS para usar uma zona DNS privada para simplificar a configuração de todos os recursos de link privados.
 
> [!IMPORTANT]
> Não é recomendável substituir uma zona que está ativamente em uso para resolver pontos finais públicos. As conexões com recursos não serão capazes de resolver corretamente sem o encaminhamento do DNS para o DNS público. Para evitar problemas, crie um nome de domínio diferente ou siga o nome sugerido para cada serviço abaixo. 
 
Para serviços Do Azure, use os nomes de região conforme descrito na tabela a seguir:

|Tipo de recurso do Private Link   |Sub-recurso  |Nome da zona  |
|---------|---------|---------|
|SQL DB (Microsoft.Sql/servidores)    |  Sql Server (sqlServer)        |   privatelink.database.windows.net       |
|Azure Synapse Analytics (Microsoft.Sql/servidores)    |  Sql Server (sqlServer)        | privatelink.database.windows.net |
|Conta de armazenamento (Microsoft.Storage/storageAccounts)    |  Blob (blob, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Conta de armazenamento (Microsoft.Storage/storageAccounts)    |    Mesa (tabela, table_secondary)      |   privatelink.table.core.windows.net       |
|Conta de armazenamento (Microsoft.Storage/storageAccounts)    |    Fila (fila, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Conta de armazenamento (Microsoft.Storage/storageAccounts)   |    Arquivo (arquivo, file_secondary)      |    privatelink.file.core.windows.net      |
|Conta de armazenamento (Microsoft.Storage/storageAccounts)     |  Web (web, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake File System Gen2 (Microsoft.Storage/storageAccounts)  |  Data Lake File System Gen2 (dfs, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft.AzureCosmosDB/banco de dadosContas)|SQL |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/banco de dadosContas)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/banco de dadosContas)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/banco de dadosContas)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/banco de dadosContas)|Tabela|privatelink.table.cosmos.azure.com|
|Banco de dados Azure para PostgreSQL - Servidor único (Microsoft.DBforPostgreSQL/servidores)|postgresqlServer|privatelink.postgres.database.azure.com|
|Banco de dados Azure para MySQL (Microsoft.DBforMySQL/servidores)|mysqlServer|privatelink.mysql.database.azure.com|
|Banco de dados Azure para MariaDB (Microsoft.DBforMariaDB/servidores)|mariadbServer|privatelink.mariadb.database.azure.com|
|Cofre de chaves do Azure (Microsoft.KeyVault/vaults)|cofre|privatelink.vaultcore.azure.net|
|Serviço Azure Kubernetes - API Kubernetes (Microsoft.ContainerService/managedClusters) | managedCluster | {guid}.privatelink. <region>Azmk8s.io.|
|Pesquisa do Azure (Microsoft.Search/searchServices)|Searchservice|privatelink.search.windows.net|   
|Registro de contêineres do Azure (Microsoft.ContainerRegistry/registros) | Registro | privatelink.azurecr.io |
|Configuração do aplicativo Azure (Microsoft.Appconfiguration/configurationStores)| configuraçãoArmazenar | privatelink.azconfig.io|
|Backup do Azure (Microsoft.RecoveryServices/vaults)| cofre |privatelink. {região}.backup.windowsazure.com|
|Azure Event Hub (Microsoft.EventHub/namespaces)| namespace |privatelink.servicebus.windows.net|
|Ônibus de serviço do Azure (Microsoft.ServiceBus/namespaces) | namespace |privatelink.servicebus.windows.net|
|Relé Azure (Microsoft.Relay/namespaces) | namespace |privatelink.servicebus.windows.net|
|Azure Event Grid (Microsoft.EventGrid/tópicos)   | topic | Tópico. {região}.privatelink.eventgrid.azure.net|
|Azure Event Grid (Microsoft.EventGrid/domínios) | domínio | Domínio. {região}.privatelink.eventgrid.azure.net |
|WebApps do Azure (Microsoft.Web/sites)    | site | privatelink.azurewebsites.net |
|Aprendizado de máquina do Azure (Microsoft.MachineLearningServices/espaços de trabalho)   | workspace | privatelink.api.azureml.ms |
 
O Azure criará um registro Canônico DNS (CNAME) no DNS público para redirecionar a resolução para os nomes de domínio sugeridos. Você poderá substituir a resolução com o endereço IP privado de seus pontos finais privados. 
 
Seus aplicativos não precisam alterar a URL de conexão. Ao tentar resolver usando um DNS público, o servidor DNS agora resolverá para seus pontos finais privados. O processo não afeta seus aplicativos. 
 
## <a name="limitations"></a>Limitações
 
A tabela a seguir inclui uma lista de limitações conhecidas ao usar pontos finais privados: 


|Limitações |Descrição |Atenuação  |
|---------|---------|---------|
|As regras do Grupo de Segurança de Rede (NSG) e as rotas definidas pelo usuário não se aplicam ao ponto final privado    |O NSG não é suportado em pontos finais privados. Embora as sub-redes que contenham o ponto final privado possam ter o NSG associado a ele, as regras não serão eficazes no tráfego processado pelo ponto final privado. Você deve ter [a aplicação de políticas de rede desativada](disable-private-endpoint-network-policy.md) para implantar pontos finais privados em uma sub-rede. O NSG ainda é aplicado em outras cargas de trabalho hospedadas na mesma sub-rede. As rotas em qualquer sub-rede do cliente usarão um prefixo /32, alterando o comportamento de roteamento padrão requer um UDR semelhante  | Controle o tráfego usando as regras do NSG para tráfego de saída em clientes de origem. Implantar rotas individuais com prefixo /32 para substituir rotas de ponto final privados. Os registros de fluxo do NSG e as informações de monitoramento das conexões de saída ainda são suportados e podem ser usados        |


## <a name="next-steps"></a>Próximas etapas
- [Criar um Ponto de Extremidade Privado para o Servidor do Banco de Dados SQL usando o portal](create-private-endpoint-portal.md)
- [Criar um Ponto de Extremidade Privado para o Servidor do Banco de Dados SQL usando o PowerShell](create-private-endpoint-powershell.md)
- [Criar um Ponto de Extremidade Privado para o Servidor do Banco de Dados SQL usando a CLI ](create-private-endpoint-cli.md)
- [Criar um Ponto de Extremidade Privado para a conta de Armazenamento usando o portal ](create-private-endpoint-storage-portal.md)
- [Criar um Ponto de Extremidade Privado para a conta do Azure Cosmos usando o portal ](../cosmos-db/how-to-configure-private-endpoints.md)
- [Criar seu próprio serviço de Link Privado usando o Azure PowerShell](create-private-link-service-powershell.md)
- [Crie seu próprio Link Privado para Banco de Dados Azure para PostgreSQL - Único servidor usando o Portal](../postgresql/howto-configure-privatelink-portal.md)
- [Crie seu próprio Link Privado para Banco de Dados Azure para PostgreSQL - Servidor único usando CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Crie seu próprio link privado para o Banco de Dados Do Azure para MySQL usando o Portal](../mysql/howto-configure-privatelink-portal.md)
- [Crie seu próprio Link Privado para Banco de Dados Azure para MySQL usando cli](../mysql/howto-configure-privatelink-cli.md)
- [Crie seu próprio Link Privado para Banco de Dados Azure para MariaDB usando o Portal](../mariadb/howto-configure-privatelink-portal.md)
- [Crie seu próprio Banco de Dados Privado para Azure para MariaDB usando cli](../mariadb/howto-configure-privatelink-cli.md)
