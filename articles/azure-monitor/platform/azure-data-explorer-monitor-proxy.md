---
title: Consultar dados em Azure Monitor usando o Data Explorer do Azure (versão prévia)
description: Use o Azure Data Explorer para executar consultas entre produtos entre o Azure Data Explorer, espaços de trabalho Log Analytics e aplicativos Application Insights clássicos no Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 2a21d7a06e8a92022b620704d1fb51a303da3ae0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185973"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Consultar dados em Azure Monitor usando o Data Explorer do Azure (versão prévia)
O cluster do proxy de Data Explorer do Azure permite executar consultas entre produtos entre o Azure Data Explorer, espaços de trabalho Log Analytics e aplicativos Application Insights clássicos no Azure Monitor. Você pode mapear Log Analytics espaços de trabalho em aplicativos Azure Monitor ou clássicos Application Insights como clusters de proxy. Você pode consultar o cluster de proxy usando as ferramentas de Data Explorer do Azure e consultá-lo em uma consulta entre clusters. O artigo mostra como se conectar a um cluster de proxy, adicionar um cluster de proxy ao Azure Data Explorer interface do usuário da Web e executar consultas em seus espaços de trabalho Log Analytics ou aplicativos Application Insights clássicos do Azure Data Explorer.

O diagrama a seguir mostra o fluxo do proxy de Data Explorer do Azure:

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-monitor-proxy-flow.png" alt-text="Fluxo de proxy do Azure data Explorer.":::


> [!NOTE]
> O proxy de Data Explorer do Azure está em visualização pública. [Conecte-se ao proxy](#connect-to-the-proxy) para habilitar o recurso de proxy para seus clusters. 

## <a name="connect-to-the-proxy"></a>Conectar-se ao proxy
Para conectar seu espaço de trabalho Log Analytics ou Application Insights aplicativo clássico, abra a[interface do usuário da Web do Azure data Explorer](https://dataexplorer.azure.com/clusters). Verifique se o seu cluster nativo do Azure Data Explorer (como o cluster de *ajuda* ) aparece no menu à esquerda antes de se conectar ao seu Log Analytics ou Application insights cluster.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Cluster nativo do Azure Data Explorer.":::

Clique em **Adicionar cluster** e adicione a URL do Log Analytics ou Application insights cluster em um dos formatos a seguir. 
    
* Por Log Analytics: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
* Por Application Insights: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

Clique em **Adicionar** para fazer a conexão.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Adicionar cluster.":::
 
> [!NOTE]
> Se você adicionar uma conexão a mais de um cluster de proxy, dê a cada um deles um nome diferente. Caso contrário, eles terão o mesmo nome no painel esquerdo.

Depois que a conexão for estabelecida, seu Log Analytics ou Application Insights cluster aparecerá no painel esquerdo com seu cluster de Data Explorer nativo do Azure. 

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics e clusters de Data Explorer do Azure.":::
 
> [!NOTE]
> O número de espaços de trabalho Azure Monitor que podem ser mapeados é limitado a 100.

## <a name="create-queries-using-azure-monitor-data"></a>Criar consultas usando dados de Azure Monitor

Você pode executar as consultas usando ferramentas de cliente que dão suporte a consultas Kusto, como: Kusto Explorer, interface do usuário da Web do Azure Data Explorer, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, lente e API REST.

> [!NOTE]
> O recurso de proxy de Data Explorer do Azure é usado somente para recuperação de dados. Para obter mais informações, consulte [suporte à função](#function-supportability).

> [!TIP]
> * O nome do banco de dados deve ter o mesmo nome que o recurso especificado no cluster de proxy. Os nomes diferenciam minúsculas e maiúsculas.
> * Em consultas entre clusters, certifique-se de que a nomenclatura de Application Insights aplicativos e espaços de trabalho do Log Analytics esteja correta.
>     * Se os nomes contiverem caracteres especiais, eles serão substituídos pela codificação de URL no nome do cluster de proxy. 
>     * Se os nomes incluírem caracteres que não atendem às [regras de nome de identificador KQL](/azure/data-explorer/kusto/query/schema-entities/entity-names), eles serão substituídos pelo caractere de traço **-** .

### <a name="direct-query-from-your-log-analytics-or-application-insights-proxy-cluster"></a>Consulta direta de seu Log Analytics ou cluster de proxy de Application Insights

Execute consultas em seu Log Analytics ou cluster de Application Insights. Verifique se o cluster está selecionado no painel esquerdo. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the Log Analaytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Consultar Log Analytics espaço de trabalho.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-proxy-cluster-and-the-azure-data-explorer-native-cluster"></a>Consulta cruzada de seu Log Analytics ou Application Insights cluster de proxy e o cluster nativo do Azure Data Explorer

Ao executar consultas entre clusters do proxy, verifique se o seu cluster nativo do Azure Data Explorer está selecionado no painel esquerdo. Os exemplos a seguir demonstram a combinação de tabelas de cluster Data Explorer do Azure usando o operador [Union](/azure/data-explorer/kusto/query/unionoperator) com um espaço de trabalho log Analytics.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```
Usar o [ `join` operador](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor), em vez de Union, pode exigir uma [dica](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor#join-hints) para executá-lo em um cluster nativo do Azure data Explorer (e não no proxy). 

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Unir dados de um cluster de Data Explorer do Azure em um locatário com um recurso de Azure Monitor em outro

Não há suporte para consultas entre locatários no proxy de Data Explorer do Azure. Você está conectado a um único locatário para executar a consulta abrangendo ambos os recursos.

Se o recurso de Data Explorer do Azure estiver no locatário ' A ' e Log Analytics espaço de trabalho estiver no locatário ' B ', use um dos dois métodos a seguir:

- O Data Explorer do Azure permite que você adicione funções para entidades de segurança em locatários diferentes. Adicione sua ID de usuário no locatário ' B ' como um usuário autorizado no cluster de Data Explorer do Azure. Valide a propriedade *[' TrustedExternalTenant '](/powershell/module/az.kusto/update-azkustocluster)* no cluster de data Explorer do Azure que contém o locatário ' B '. Execute a consulta cruzada totalmente no locatário ' B '.

- Use [Lighthouse](../../lighthouse/index.yml) para projetar o recurso Azure monitor no locatário ' A '.

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Conectar-se ao Azure Data Explorer clusters de diferentes locatários

O Kusto Explorer entra automaticamente no locatário ao qual a conta de usuário pertence originalmente. Para acessar recursos em outros locatários com a mesma conta de usuário, o `tenantId` deve ser especificado explicitamente na cadeia de conexão: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **tenantid**

## <a name="function-supportability"></a>Suporte à função

O cluster do proxy de Data Explorer do Azure dá suporte a funções para Log Analytics e Application Insights. Esse recurso permite que as consultas entre clusters façam referência a uma função tabular Azure Monitor diretamente.

Os comandos a seguir têm suporte pelo proxy:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

A imagem a seguir ilustra um exemplo de consulta de uma função de tabela da interface do usuário da Web do Azure Data Explorer. Para usar a função, execute o nome na janela de consulta.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Consultar uma função de tabela do Azure Data Explorer interface do usuário da Web.":::
 
> [!NOTE]
> Azure Monitor só dá suporte a funções de tabela, que não dão suporte a parâmetros.

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
- Aprenda a [escrever consultas no Azure data Explorer](/azure/data-explorer/write-queries).