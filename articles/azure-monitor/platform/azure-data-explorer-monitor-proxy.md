---
title: Consultar dados em Azure Monitor usando o Data Explorer do Azure (versão prévia)
description: Use o Azure Data Explorer para executar consultas entre produtos entre o Azure Data Explorer, espaços de trabalho Log Analytics e aplicativos Application Insights clássicos no Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: dd29b18dda46a00966a0e5adea7e06be8f43ad35
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444944"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Consultar dados em Azure Monitor usando o Data Explorer do Azure (versão prévia)

O Data Explorer do Azure dá suporte a consultas entre serviços entre o Azure Data Explorer, [Application insights (ia)](/azure/azure-monitor/app/app-insights-overview)e [log Analytics (la)](/azure/azure-monitor/platform/data-platform-logs). Em seguida, você pode consultar seu espaço de trabalho Log Analytics/Application Insights usando as ferramentas de Data Explorer do Azure e consultá-lo em uma consulta entre serviços. O artigo mostra como fazer uma consulta entre serviços e como adicionar o espaço de trabalho Log Analytics/Application Insights à interface do usuário da Web do Azure Data Explorer.

O fluxo de consultas entre serviços do Azure Data Explorer: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="fluxo de proxy do Azure data Explorer.":::

> [!NOTE]
> * A capacidade de consultar Azure Monitor dados do Data Explorer do Azure, diretamente das ferramentas de cliente do Azure Data Explorer, ou indiretamente, executando uma consulta em um cluster de Data Explorer do Azure, está no modo de visualização.
>* Entre em contato com a equipe de [consulta entre serviços](mailto:adxproxy@microsoft.com) com perguntas.

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Adicionar um espaço de trabalho Log Analytics/Application Insights às ferramentas de cliente do Data Explorer do Azure

1. Verifique se o seu cluster nativo do Azure Data Explorer (como o cluster de *ajuda* ) aparece no menu à esquerda antes de se conectar ao seu Log Analytics ou Application insights cluster.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Cluster nativo do Azure Data Explorer.":::

 Na interface do usuário do Data Explorer do Azure ( https://dataexplorer.azure.com/clusters) , selecione **Adicionar cluster**.

2. Na janela **Adicionar cluster** , adicione a URL do cluster la ou ia.

    * Para LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Para ia: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Selecione **Adicionar**.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Adicionar cluster.":::
 
>[!NOTE]
>Se você adicionar uma conexão a mais de um espaço de trabalho do Log Analytics/Application insights, dê a cada um um nome diferente. Caso contrário, eles terão o mesmo nome no painel esquerdo.

 Depois que a conexão for estabelecida, seu Log Analytics ou Application Insights espaço de trabalho aparecerá no painel esquerdo com seu cluster de Data Explorer nativo do Azure.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics e clusters de Data Explorer do Azure.":::
 
> [!NOTE]
> O número de espaços de trabalho Azure Monitor que podem ser mapeados é limitado a 100.

## <a name="create-queries-using-azure-monitor-data"></a>Criar consultas usando dados de Azure Monitor

Você pode executar as consultas usando ferramentas de cliente que dão suporte a consultas Kusto, como: Kusto Explorer, interface do usuário da Web do Azure Data Explorer, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, lente, API REST.

> [!NOTE]
> A capacidade de consulta entre serviços é usada apenas para recuperação de dados. Para obter mais informações, consulte [suporte à função](#function-supportability).

> [!TIP]
> * O nome do banco de dados deve ter o mesmo nome que o recurso especificado na consulta entre serviços. Os nomes diferenciam minúsculas e maiúsculas.
> * Em consultas entre clusters, certifique-se de que a nomenclatura de Application Insights aplicativos e espaços de trabalho do Log Analytics esteja correta.
> * Se os nomes contiverem caracteres especiais, eles serão substituídos pela codificação de URL na consulta de serviço cruzado.
> * Se os nomes incluírem caracteres que não atendem às [regras de nome de identificador KQL](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names), eles serão substituídos pelo caractere de traço **-** .

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Consulta direta em seus espaços de trabalho de Log Analytics ou Application Insights de ferramentas de cliente do Azure Data Explorer

Execute consultas em seus espaços de trabalho Log Analytics ou Application Insights. Verifique se o espaço de trabalho está selecionado no painel esquerdo.
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Consultar Log Analytics espaço de trabalho.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Consulta cruzada de seu Log Analytics ou Application Insights e o cluster nativo do Azure Data Explorer

Ao executar consultas de serviço de cluster cruzado, verifique se o seu cluster nativo do Azure Data Explorer está selecionado no painel esquerdo. Os exemplos a seguir demonstram a combinação de tabelas de cluster Data Explorer do Azure [usando Union](/azure/data-explorer/kusto/query/unionoperator) com o espaço de trabalho log Analytics.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Consulta entre serviços do Data Explorer do Azure.":::

Usar o [ `join` operador](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator), em vez de Union, pode exigir um [`hint`](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator#join-hints) para executá-lo em um cluster nativo do Azure data Explorer.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Unir dados de um cluster de Data Explorer do Azure em um locatário com um recurso de Azure Monitor em outro

Não há suporte para consultas entre locatários entre os serviços. Você está conectado a um único locatário para executar a consulta abrangendo ambos os recursos.

Se o recurso de Data Explorer do Azure estiver no locatário ' A ' e Log Analytics espaço de trabalho estiver no locatário ' B ', use um dos dois métodos a seguir:

1. O Data Explorer do Azure permite que você adicione funções para entidades de segurança em locatários diferentes. Adicione sua ID de usuário no locatário ' B ' como um usuário autorizado no cluster de Data Explorer do Azure. Valide a propriedade *[' TrustedExternalTenant '](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* no cluster de data Explorer do Azure que contém o locatário ' B '. Execute a consulta cruzada totalmente no locatário ' B '.

2. Use [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) para projetar o recurso Azure monitor no locatário ' A '.
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Conectar-se ao Azure Data Explorer clusters de diferentes locatários

O Kusto Explorer entra automaticamente no locatário ao qual a conta de usuário pertence originalmente. Para acessar recursos em outros locatários com a mesma conta de usuário, o `tenantId` deve ser especificado explicitamente na cadeia de conexão: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **tenantid**

## <a name="function-supportability"></a>Suporte à função

As consultas de serviço cruzado Data Explorer do Azure dão suporte a funções para Application Insights e Log Analytics.
Esse recurso permite que as consultas entre clusters façam referência a uma função tabular Azure Monitor diretamente.
Os comandos a seguir têm suporte com a consulta de serviço cruzado:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

A imagem a seguir ilustra um exemplo de consulta de uma função de tabela da interface do usuário da Web do Azure Data Explorer.
Para usar a função, execute o nome na janela de consulta.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Consultar uma função de tabela do Azure Data Explorer interface do usuário da Web.":::

## <a name="additional-syntax-examples"></a>Exemplos de sintaxe adicionais

As seguintes opções de sintaxe estão disponíveis ao chamar os clusters Log Analytics ou Application Insights:

|Descrição da sintaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Banco de dados dentro de um cluster que contém apenas o recurso definido nesta assinatura (**recomendado para consultas entre clusters**) |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| Cluster que contém todos os aplicativos/espaços de trabalho nesta assinatura    |     cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|Cluster que contém todos os aplicativos/espaços de trabalho na assinatura e que são membros deste grupo de recursos    |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|Cluster que contém apenas o recurso definido nesta assinatura      |    cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre a [estrutura de dados dos espaços de trabalho log Analytics e Application insights](data-platform-logs.md).
- Aprenda a [escrever consultas no Azure data Explorer](https://docs.microsoft.com/azure/data-explorer/write-queries).