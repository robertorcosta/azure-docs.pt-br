---
title: Como auditar Azure Cosmos DB operações do plano de controle
description: Saiba como auditar as operações do plano de controle, como adicionar uma região, taxa de transferência de atualização, failover de região, adicionar uma VNet, etc. no Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 32dd598b8fc62c0ec68f86f95b02f9f3d98cedd2
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116291"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Como auditar Azure Cosmos DB operações do plano de controle

O plano de controle no Azure Cosmos DB é um serviço RESTful que permite que você execute um conjunto variado de operações na conta do Azure Cosmos. Ele expõe um modelo de recurso público (por exemplo: Database, Account) e várias operações para os usuários finais executarem ações no modelo de recurso. As operações do plano de controle incluem alterações no contêiner ou na conta do Azure Cosmos. Por exemplo, operações como criar uma conta do Azure Cosmos, adicionar uma região, atualizar taxa de transferência, failover de região, adicionar uma VNet etc. são algumas das operações de plano de controle. Este artigo explica como auditar as operações do plano de controle no Azure Cosmos DB. Você pode executar as operações do plano de controle em contas do Azure Cosmos usando CLI do Azure, PowerShell ou portal do Azure, enquanto para contêineres, use CLI do Azure ou PowerShell.

Veja a seguir alguns cenários de exemplo em que as operações do plano de controle de auditoria são úteis:

* Você deseja receber um alerta quando as regras de firewall para sua conta do Azure cosmos são modificadas. O alerta é necessário para localizar modificações não autorizadas em regras que regem a segurança de rede de sua conta do Azure Cosmos e tomar uma ação rápida.

* Você deseja receber um alerta se uma nova região for adicionada ou removida da sua conta do Azure Cosmos. Adicionar ou remover regiões tem implicações em requisitos de cobrança e da soberania de dados. Este alerta o ajudará a detectar uma adição acidental ou remoção de região em sua conta.

* Você deseja obter mais detalhes dos logs de diagnóstico sobre o que mudou. Por exemplo, uma VNet foi alterada.

## <a name="disable-key-based-metadata-write-access"></a>Desabilitar acesso de gravação de metadados com base em chave

Antes de auditar as operações do plano de controle no Azure Cosmos DB, desabilite o acesso de gravação de metadados baseado em chave em sua conta. Quando o acesso de gravação de metadados com base em chave está desabilitado, os clientes que se conectam à conta do Azure Cosmos por meio de chaves de conta são impedidos de acessar Você pode desabilitar o acesso de gravação definindo `disableKeyBasedMetadataWriteAccess` a propriedade como true. Depois de definir essa propriedade, as alterações em qualquer recurso podem ocorrer de um usuário com a função e as credenciais adequadas do RBAC (controle de acesso baseado em função). Para saber mais sobre como definir essa propriedade, consulte o artigo [impedindo alterações de SDKs](role-based-access-control.md#preventing-changes-from-cosmos-sdk) . Depois de desabilitar o acesso de gravação, as alterações baseadas no SDK para a taxa de transferência, índice continuarão a funcionar.

Considere os seguintes pontos ao desativar o acesso de gravação de metadados:

* Avalie e verifique se seus aplicativos não fazem chamadas de metadados que alteram os recursos acima (por exemplo, criar coleção, atualizar taxa de transferência,...) usando o SDK ou as chaves de conta.

* Atualmente, o portal do Azure usa chaves de conta para operações de metadados e, portanto, essas operações serão bloqueadas. Como alternativa, use as implantações de modelo CLI do Azure, SDKs ou Resource Manager para executar essas operações.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Habilitar logs de diagnóstico para operações de plano de controle

Você pode habilitar os logs de diagnóstico para operações de plano de controle usando o portal do Azure. Depois de habilitar, os logs de diagnóstico registrarão a operação como um par de eventos de início e de conclusão com detalhes relevantes. Por exemplo, *RegionFailoverStart* e *RegionFailoverComplete* concluirão o evento de failover de região.

Use as etapas a seguir para habilitar o registro em log nas operações do plano de controle:

1. Entre [portal do Azure](https://portal.azure.com) e navegue até sua conta do Azure Cosmos.

1. Abra o painel **configurações de diagnóstico** , forneça um **nome** para os logs a serem criados.

1. Selecione **ControlPlaneRequests** para tipo de log e selecione a opção **Enviar para log Analytics** .

Você também pode armazenar os logs em uma conta de armazenamento ou transmitir para um hub de eventos. Este artigo mostra como enviar logs para o log Analytics e, em seguida, consultá-los. Depois de habilitar, levará alguns minutos para que os logs de diagnóstico entrem em vigor. Todas as operações do plano de controle executadas após esse ponto podem ser controladas. A captura de tela a seguir mostra como habilitar os logs do plano de controle:

![Habilitar log de solicitações de plano de controle](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>Exibir as operações do plano de controle

Depois de ativar o registro em log, use as seguintes etapas para rastrear as operações para uma conta específica:

1. Entre no [portal do Azure](https://portal.azure.com).

1. Abra a guia **Monitor** na navegação esquerda e, em seguida, selecione o painel **logs** . Ele abre uma interface do usuário na qual você pode facilmente executar consultas com essa conta específica no escopo. Execute a consulta a seguir para exibir os logs do plano de controle:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

As capturas de tela a seguir capturam logs quando uma VNET é adicionada a uma conta do Azure Cosmos:

![Controlar os logs de plano quando uma VNet for adicionada](./media/audit-control-plane-logs/add-ip-filter-logs.png)

As capturas de tela a seguir capturam logs quando a taxa de transferência de uma tabela Cassandra é atualizada:

![Controlar os logs de plano quando a taxa de transferência for atualizada](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identificar a identidade associada a uma operação específica

Se você quiser depurar ainda mais, poderá identificar uma operação específica no log de **atividades** usando a ID da atividade ou o carimbo de data/hora da operação. O carimbo de data/hora é usado para alguns clientes do Resource Manager em que a ID da atividade não é aprovada explicitamente. O log de atividades fornece detalhes sobre a identidade com a qual a operação foi iniciada. A captura de tela a seguir mostra como usar a ID da atividade e localizar as operações associadas a ela no log de atividades:

![Usar a ID da atividade e localizar as operações](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Operações do plano de controle para a conta do Azure Cosmos

A seguir estão as operações do plano de controle disponíveis no nível da conta. A maioria das operações é controlada no nível da conta. Essas operações estão disponíveis como métricas no Azure monitor:

* Região adicionada
* Região removida
* Conta excluída
* Falha na região
* Conta criada
* Rede virtual excluída
* Configurações de rede da conta atualizadas
* Configurações de replicação de conta atualizadas
* Chaves de conta atualizadas
* Configurações de backup da conta atualizadas
* Configurações de diagnóstico da conta atualizadas

## <a name="control-plane-operations-for-database-or-containers"></a>Operações de plano de controle para banco de dados ou contêineres

A seguir estão as operações do plano de controle disponíveis no nível do banco de dados e do contêiner. Essas operações estão disponíveis como métricas no Azure monitor:

* Banco de dados SQL atualizado
* Contêiner SQL atualizado
* Produtividade do banco de dados SQL atualizada
* Produtividade do contêiner SQL atualizada
* Banco de dados SQL excluído
* Contêiner SQL excluído
* Cassandra keyspace atualizado
* Tabela Cassandra atualizada
* Taxa de transferência de keyspace Cassandra atualizada
* Taxa de transferência de tabela Cassandra atualizada
* Cassandra keyspace excluído
* Tabela Cassandra excluída
* Banco de dados Gremlin atualizado
* Grafo Gremlin atualizado
* Taxa de transferência do banco de dados Gremlin atualizada
* Taxa de transferência do grafo Gremlin atualizada
* Banco de dados Gremlin excluído
* Grafo Gremlin excluído
* Banco de dados Mongo atualizado
* Coleção Mongo atualizada
* Taxa de transferência do banco de dados Mongo atualizada
* Taxa de transferência de coleção Mongo atualizada
* Banco de dados Mongo excluído
* Coleção Mongo excluída
* Tabela do azuretable atualizada
* Taxa de transferência de tabela do azuretable atualizada
* Tabela do azuretable excluída

## <a name="diagnostic-log-operations"></a>Operações de log de diagnóstico

A seguir estão os nomes de operação nos logs de diagnóstico para operações diferentes:

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

Para operações específicas de API, a operação é nomeada com o seguinte formato:

* ApiKind + ApiKindResourceType + OperationType + iniciar/concluir
* ApiKind + ApiKindResourceType + "taxa de transferência" + OperationType + iniciar/concluir

**Exemplo** 

* CassandraKeyspacesUpdateStart, CassandraKeyspacesUpdateComplete
* CassandraKeyspacesThroughputUpdateStart, CassandraKeyspacesThroughputUpdateComplete

A propriedade *ResourceDetails* contém o corpo do recurso inteiro como uma carga de solicitação e contém todas as propriedades solicitadas para atualização

## <a name="next-steps"></a>Próximas etapas

* [Explorar Azure Monitor para Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Monitorar e depurar com métricas no Azure Cosmos DB](use-metrics.md)
