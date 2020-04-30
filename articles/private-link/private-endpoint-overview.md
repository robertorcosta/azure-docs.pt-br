---
title: O que é um ponto de extremidade privado do Azure?
description: Saiba mais sobre o ponto de extremidade privado do Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: a4117acb2fada5c4422e177e9e6b84d7a0a51b69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82129314"
---
# <a name="what-is-azure-private-endpoint"></a>O que é o ponto de extremidade privado do Azure?

O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura a um serviço com tecnologia do Link Privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente o serviço em sua VNet. O serviço pode ser um serviço do Azure, como o armazenamento do Azure, Azure Cosmos DB, SQL, etc. ou seu próprio [serviço de link privado](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Propriedades do ponto de extremidade privado 
 Um ponto de extremidade privado especifica as seguintes propriedades: 


|Propriedade  |Descrição |
|---------|---------|
|Nome    |    Um nome exclusivo dentro do grupo de recursos.      |
|Sub-rede    |  A sub-rede para implantar e alocar endereços IP privados de uma rede virtual. Para obter os requisitos de sub-rede, consulte a seção limitações neste artigo.         |
|Recurso de link privado    |   O recurso de link privado para se conectar usando a ID de recurso ou alias da lista de tipos disponíveis. Um identificador de rede exclusivo será gerado para todo o tráfego enviado para esse recurso.       |
|Subrecurso de destino   |      O subrecurso a ser conectado. Cada tipo de recurso de link privado tem opções diferentes para selecionar com base na preferência.    |
|Método de aprovação de conexão    |  Automático ou manual. Com base nas permissões de RBAC (controle de acesso baseado em função), seu ponto de extremidade privado pode ser aprovado automaticamente. Se você tentar se conectar a um recurso de link privado sem RBAC, use o método manual para permitir que o proprietário do recurso aprove a conexão.        |
|Mensagem de Solicitação     |  Você pode especificar uma mensagem para que as conexões solicitadas sejam aprovadas manualmente. Essa mensagem pode ser usada para identificar uma solicitação específica.        |
|Status da conexão   |   Uma propriedade somente leitura que especifica se o ponto de extremidade privado está ativo. Somente pontos de extremidade privados em um Estado aprovado podem ser usados para enviar tráfego. Outros Estados disponíveis: <br>-**Aprovado**: a conexão foi aprovada automaticamente ou manualmente e está pronta para ser usada.</br><br>-**Pendente**: a conexão foi criada manualmente e está aguardando a aprovação do proprietário do recurso de link privado.</br><br>-**Rejeitado**: a conexão foi rejeitada pelo proprietário do recurso de link privado.</br><br>-**Desconectado**: a conexão foi removida pelo proprietário do recurso de link privado. O ponto de extremidade privado se torna informativo e deve ser excluído para limpeza. </br>|

Aqui estão alguns detalhes importantes sobre pontos de extremidade privados: 
- O ponto de extremidade privado permite a conectividade entre os consumidores da mesma VNet, VNets emparelhadas de modo global e no local usando [VPN](https://azure.microsoft.com/services/vpn-gateway/) ou [rota expressa](https://azure.microsoft.com/services/expressroute/) e serviços fornecidos por um link privado.
 
- Ao criar um ponto de extremidade privado, uma interface de rede também é criada para o ciclo de vida do recurso. A interface recebe um endereço IP privado da sub-rede que mapeia para o serviço de vínculo privado.
 
- O ponto de extremidade privado deve ser implantado na mesma região que a rede virtual. 
 
- O recurso de link privado pode ser implantado em uma região diferente da rede virtual e do ponto de extremidade privado.
 
- Vários pontos de extremidade privados podem ser criados usando o mesmo recurso de link privado. Para uma única rede usando uma configuração de servidor DNS comum, a prática recomendada é usar um único ponto de extremidade privado para um determinado recurso de link privado para evitar entradas duplicadas ou conflitos na resolução de DNS. 
 
- Vários pontos de extremidade privados podem ser criados nas mesmas ou em sub-redes diferentes na mesma rede virtual. Há limites para o número de pontos de extremidade privados que você pode criar em uma assinatura. Para obter detalhes, consulte [limites do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


 
## <a name="private-link-resource"></a>Recurso de link privado 
Um recurso de link privado é o destino de destino de um determinado ponto de extremidade particular. A seguir está uma lista de tipos de recursos de link privado disponíveis: 
 
|Nome do recurso do link privado  |Tipo de recurso   |Sub-recursos  |
|---------|---------|---------|
|**Serviço de vínculo privado** (seu próprio serviço)   |  Microsoft. Network/privateLinkServices       | vazio |
|**Banco de Dados SQL do Azure** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        | 
|**Armazenamento do Azure**  | Microsoft.Storage/storageAccounts    |  BLOB (BLOB, blob_secondary)<BR> Tabela (tabela, table_secondary)<BR> Fila (fila, queue_secondary)<BR> Arquivo (arquivo, file_secondary)<BR> Web (Web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  BLOB (BLOB, blob_secondary)<BR> Data Lake sistema de arquivos Gen2 (DFS, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft. AzureCosmosDB/databaseAccounts    | SQL, MongoDB, Cassandra, Gremlin, tabela|
|**Banco de dados do Azure para PostgreSQL-servidor único** | Microsoft.DBforPostgreSQL/servers    | postgresqlServer |
|**Banco de Dados do Azure para MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Banco de Dados do Azure para MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Cofre da Chave do Azure** | Microsoft.KeyVault/vaults    | cofre |
|**Serviço de Kubernetes do Azure – API Kubernetes** | Microsoft.ContainerService/managedClusters    | managedCluster |
|**Azure Search** | Microsoft. Search/searchService| searchService|  
|**Registro de Contêiner do Azure** | Microsoft.ContainerRegistry/registries    | Registro |
|**Configuração de Aplicativos do Azure** | Microsoft. Appconfiguration/configurationStores    | configurationStore |
|**Backup do Azure** | Microsoft.RecoveryServices/vaults    | cofre |
|**Hub de Eventos do Azure** | Microsoft.EventHub/namespaces    | namespace |
|**Barramento de Serviço do Azure** | Microsoft.ServiceBus/namespaces | namespace |
|**Retransmissão do Azure** | Microsoft.Relay/namespaces | namespace |
|**Grade de Eventos do Azure** | Microsoft.EventGrid/topics    | topic |
|**Grade de Eventos do Azure** | Microsoft. EventGrid/domínios    | domínio |
|**Webapps do Azure** | Microsoft.Web/sites    | site |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces    | workspace |
  
 
## <a name="network-security-of-private-endpoints"></a>Segurança de rede de pontos de extremidade privados 
Ao usar pontos de extremidade privados para serviços do Azure, o tráfego é protegido para um recurso de link particular específico. A plataforma executa um controle de acesso para validar conexões de rede que atingem apenas o recurso de link particular especificado. Para acessar recursos adicionais dentro do mesmo serviço do Azure, são necessários pontos de extremidade privados adicionais. 
 
Você pode bloquear completamente suas cargas de trabalho de acessar pontos de extremidade públicos para se conectar a um serviço do Azure com suporte. Esse controle fornece uma camada de segurança de rede adicional para seus recursos, fornecendo uma proteção interna do vazamento que impede o acesso a outros recursos hospedados no mesmo serviço do Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Acesso a um recurso de link privado usando o fluxo de trabalho de aprovação 
Você pode se conectar a um recurso de link privado usando os seguintes métodos de aprovação de conexão:
- Aprovado **automaticamente** quando você possui ou tem permissão no recurso de link particular específico. A permissão necessária é baseada no tipo de recurso de link privado no seguinte formato: Microsoft. \<> do provedor/<resource_type>/privateendpointconnectionapproval/Action
- Solicitação **manual** quando você não tem a permissão necessária e deseja solicitar acesso. Um fluxo de trabalho de aprovação será iniciado. O ponto de extremidade privado e a conexão do ponto de extremidade particular subsequente serão criados em um estado "Pendente". O proprietário do recurso de link privado é responsável por aprovar a conexão. Depois de aprovado, o ponto de extremidade privado é habilitado para enviar o tráfego normalmente, conforme mostrado no diagrama de fluxo de trabalho de aprovação a seguir.  

![aprovação do fluxo de trabalho](media/private-endpoint-overview/private-link-paas-workflow.png)
 
O proprietário do recurso de link privado pode executar as seguintes ações em uma conexão de ponto de extremidade particular: 
- Examine todos os detalhes de conexões do ponto de extremidade privado. 
- Aprove uma conexão de ponto de extremidade particular. O ponto de extremidade privado correspondente será habilitado para enviar o tráfego para o recurso de link privado. 
- Rejeite uma conexão de ponto de extremidade privada. O ponto de extremidade privado correspondente será atualizado para refletir o status.
- Exclua uma conexão de ponto de extremidade particular em qualquer Estado. O ponto de extremidade privado correspondente será atualizado com um estado desconectado para refletir a ação, o proprietário do ponto de extremidade privado só poderá excluir o recurso neste ponto. 
 
> [!NOTE]
> Somente um ponto de extremidade privado em um Estado aprovado pode enviar tráfego para um determinado recurso de link privado. 

### <a name="connecting-using-alias"></a>Conectando usando alias
Alias é um moniker exclusivo gerado quando o proprietário do serviço cria o serviço de vínculo privado por trás de um balanceador de carga padrão. O proprietário do serviço pode compartilhar esse alias com seus consumidores offline. Os consumidores podem solicitar uma conexão com o serviço de vínculo privado usando o URI do recurso ou o alias. Se você quiser se conectar usando o alias, deverá criar um ponto de extremidade privado usando o método de aprovação de conexão manual. Para usar o método de aprovação de conexão manual, defina o parâmetro de solicitação manual como true durante o fluxo de criação do ponto de extremidade privado. Examine [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) e [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) para obter detalhes. 

## <a name="dns-configuration"></a>Configuração de DNS 
Ao se conectar a um recurso de link privado usando um FQDN (nome de domínio totalmente qualificado) como parte da cadeia de conexão, é importante definir corretamente as configurações de DNS para resolver o endereço IP privado alocado. Os serviços do Azure existentes já podem ter uma configuração de DNS para usar ao se conectar por meio de um ponto de extremidade público. Isso precisa ser substituído para se conectar usando seu ponto de extremidade privado. 
 
O adaptador de rede associado ao ponto de extremidade privado contém o conjunto completo de informações necessárias para configurar o DNS, incluindo endereços IP privados e FQDN alocados para um determinado recurso de link privado. 

Para obter informações detalhadas completas sobre práticas recomendadas e recomendações para configurar o DNS para pontos de extremidade privados, examine o [artigo configuração de DNS do ponto de extremidade privado](private-endpoint-dns.md).



 
## <a name="limitations"></a>Limitações
 
A tabela a seguir inclui uma lista de limitações conhecidas ao usar pontos de extremidade privados: 


|Limitações |Descrição |Atenuação  |
|---------|---------|---------|
|As regras do NSG (grupo de segurança de rede) e as rotas definidas pelo usuário não se aplicam ao ponto de extremidade privado    |Não há suporte para NSG em pontos de extremidade privados. Embora as sub-redes que contenham o ponto de extremidade privado possam ter NSG associado a ela, as regras não serão efetivas no tráfego processado pelo ponto de extremidade privado. Você deve ter [imposição de políticas de rede desabilitada](disable-private-endpoint-network-policy.md) para implantar pontos de extremidade privados em uma sub-rede. O NSG ainda é imposto em outras cargas de trabalho hospedadas na mesma sub-rede. As rotas em qualquer sub-rede do cliente usarão um prefixo/32, alterando o comportamento de roteamento padrão requer um UDR semelhante  | Controle o tráfego usando regras de NSG para o tráfego de saída em clientes de origem. Implante rotas individuais com o prefixo/32 para substituir rotas de ponto de extremidade particulares. Logs de fluxo NSG e informações de monitoramento para conexões de saída ainda têm suporte e podem ser usadas        |


## <a name="next-steps"></a>Próximas etapas
- [Criar um Ponto de Extremidade Privado para o Servidor do Banco de Dados SQL usando o portal](create-private-endpoint-portal.md)
- [Criar um Ponto de Extremidade Privado para o Servidor do Banco de Dados SQL usando o PowerShell](create-private-endpoint-powershell.md)
- [Criar um Ponto de Extremidade Privado para o Servidor do Banco de Dados SQL usando a CLI ](create-private-endpoint-cli.md)
- [Criar um Ponto de Extremidade Privado para a conta de Armazenamento usando o portal ](create-private-endpoint-storage-portal.md)
- [Criar um Ponto de Extremidade Privado para a conta do Azure Cosmos usando o portal ](../cosmos-db/how-to-configure-private-endpoints.md)
- [Criar seu próprio serviço de Link Privado usando o Azure PowerShell](create-private-link-service-powershell.md)
- [Criar seu próprio link privado para o banco de dados do Azure para PostgreSQL-servidor único usando o portal](../postgresql/howto-configure-privatelink-portal.md)
- [Criar seu próprio link privado para o banco de dados do Azure para PostgreSQL-servidor único usando a CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Criar seu próprio link privado para o banco de dados do Azure para MySQL usando o portal](../mysql/howto-configure-privatelink-portal.md)
- [Criar seu próprio link privado para o banco de dados do Azure para MySQL usando a CLI](../mysql/howto-configure-privatelink-cli.md)
- [Criar seu próprio link privado para o banco de dados do Azure para MariaDB usando o portal](../mariadb/howto-configure-privatelink-portal.md)
- [Criar seu próprio link privado para o banco de dados do Azure para MariaDB usando a CLI](../mariadb/howto-configure-privatelink-cli.md)
