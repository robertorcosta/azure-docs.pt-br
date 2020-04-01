---
title: Como auditar as operações do avião de controle Azure Cosmos DB
description: Aprenda a auditar as operações do plano de controle, como adicionar uma região, atualizar throughput, failover da região, adicionar um VNet etc. no Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 64ad8e6b1101d8486268c857b3a7752e1801f52c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420276"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Como auditar as operações do avião de controle Azure Cosmos DB

As operações do avião de controle incluem alterações na conta ou contêiner do Azure Cosmos. Por exemplo, criar uma conta do Azure Cosmos, adicionar uma região, atualizar throughput, failover da região, adicionar um VNet etc. são algumas das operações do avião de controle. Este artigo explica como auditar as operações do avião de controle no Azure Cosmos DB.

## <a name="disable-key-based-metadata-write-access"></a>Desativar acesso de gravação de metadados baseado em chave
 
Antes de auditar as operações do avião de controle no Azure Cosmos DB, desative o acesso de gravação de metadados baseado em chaves em sua conta. Quando o acesso à gravação de metadados baseado em chave é desativado, os clientes que se conectam à conta do Azure Cosmos através das chaves da conta são impedidos de acessar a conta. Você pode desativar o acesso `disableKeyBasedMetadataWriteAccess` de gravação definindo a propriedade como true. Depois de definir essa propriedade, as alterações em qualquer recurso podem acontecer a partir de um usuário com a função rbac (controle de acesso baseado em função) adequada e apenas credenciais. Para saber mais sobre como definir essa propriedade, consulte as alterações de prevenção do artigo [SDKs.](role-based-access-control.md#preventing-changes-from-cosmos-sdk)

 Considere os seguintes pontos ao desativar o acesso de gravação de metadados:

* Avalie e garanta que seus aplicativos não façam chamadas de metadados que alterem os recursos acima (Por exemplo, crie a coleta, atualize throughput, ...) usando as chaves do SDK ou da conta.

* Atualmente, o portal Azure usa chaves de conta para operações de metadados e, portanto, essas operações serão bloqueadas. Alternativamente, use as implantações de modelo azure CLI, SDKs ou Resource Manager para executar tais operações.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Habilite registros de diagnóstico para operações de plano de controle

Você pode habilitar registros de diagnóstico para operações de plano de controle usando o portal Azure. Use as seguintes etapas para permitir o registro em operações de plano de controle:

1. Entre no [portal Azure](https://portal.azure.com) e navegue até sua conta do Azure Cosmos.

1. Abra o painel **de configurações de diagnóstico,** forneça um **Nome** para os logs a serem criados.

1. Selecione **ControlPlaneRequests** para o tipo de log e selecione a opção **Enviar para log analytics.**

Você também pode armazenar os logs em uma conta de armazenamento ou transmitir para um centro de eventos. Este artigo mostra como enviar logs para análises de log e, em seguida, consulta-los. Depois de habilitar, leva alguns minutos para os registros de diagnóstico entrarem em vigor. Todas as operações do avião de controle realizadas após esse ponto podem ser rastreadas. A captura de tela a seguir mostra como ativar registros de plano de controle:

![Habilitar o registro de solicitações de plano de controle](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>Veja as operações do avião de controle

Depois de ativar o registro, use as seguintes etapas para rastrear as operações de uma conta específica:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Abra a guia **Monitor** a partir da navegação à esquerda e selecione o painel **Logs.** Ele abre uma ui onde você pode facilmente executar consultas com essa conta específica no escopo. Execute a seguinte consulta para exibir registros de plano de controle:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

As capturas de tela a seguir capturam registros quando um VNET é adicionado a uma conta do Azure Cosmos:

![Controle os registros do avião quando um VNet é adicionado](./media/audit-control-plane-logs/add-ip-filter-logs.png)

As capturas de tela a seguir capturam logs quando o throughput de uma tabela Cassandra é atualizado:

![Registros do plano de controle quando o throughput é atualizado](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identifique a identidade associada a uma operação específica

Se você quiser depurar ainda mais, você pode identificar uma operação específica no **registro atividade** usando o ID de atividade ou pelo carimbo de data da operação. O carimbo de tempo é usado para alguns clientes do Gerenciador de Recursos onde o ID de atividade não é explicitamente passado. O registro atividade fornece detalhes sobre a identidade com que a operação foi iniciada. A captura de tela a seguir mostra como usar o ID de atividade e encontrar as operações associadas a ele no registro Atividade:

![Use o ID de atividade e encontre as operações](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="next-steps"></a>Próximas etapas

* [Explore o Monitor Azure para Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Monitorar e depurar com métricas no Azure Cosmos DB](use-metrics.md)