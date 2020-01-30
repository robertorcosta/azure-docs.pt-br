---
title: Consultar dados em Azure Monitor usando o Data Explorer do Azure (versão prévia)
description: Neste tópico, consulte dados em Azure Monitor criando um proxy de Data Explorer do Azure para consultas entre produtos com Application Insights e Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: d39ffa05448600fe3bd09baf6080aa1565ae19ba
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843558"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Consultar dados em Azure Monitor usando o Data Explorer do Azure (versão prévia)

O cluster de proxy de Data Explorer do Azure (proxy ADX) é uma entidade que permite executar consultas entre produtos entre o Azure Data Explorer, [Application insights (ia)](/azure/azure-monitor/app/app-insights-overview)e [log Analytics (la)](/azure/azure-monitor/platform/data-platform-logs) no serviço [Azure monitor](/azure/azure-monitor/) . Você pode mapear Azure Monitor Log Analytics espaços de trabalho ou Application Insights aplicativos como clusters de proxy. Você pode consultar o cluster de proxy usando as ferramentas de Data Explorer do Azure e consultá-lo em uma consulta entre clusters. O artigo mostra como se conectar a um cluster de proxy, adicionar um cluster de proxy ao Azure Data Explorer interface do usuário da Web e executar consultas em seus aplicativos de AI ou espaços de trabalho da LA do Azure Data Explorer.

O fluxo do proxy de Data Explorer do Azure: 

![Fluxo de proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> O proxy ADX está no modo de visualização. [Conecte-se ao proxy](#connect-to-the-proxy) para habilitar o recurso de proxy ADX para seus clusters. Entre em contato com a equipe do [ADXProxy](mailto:adxproxy@microsoft.com) com dúvidas.

## <a name="connect-to-the-proxy"></a>Conectar-se ao proxy

1. Verifique se o seu cluster nativo do Azure Data Explorer (como o cluster de *ajuda* ) aparece no menu à esquerda antes de se conectar ao seu Log Analytics ou Application insights cluster.

    ![Cluster nativo do ADX](media/adx-proxy/web-ui-help-cluster.png)

1. Na interface do usuário do Data Explorer do Azure (https://dataexplorer.azure.com/clusters), selecione **Adicionar cluster**.

1. Na janela **Adicionar cluster** , adicione a URL ao cluster la ou ia. 
    
    * Para LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Para ia: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Selecione **Adicionar**.

    ![Adicionar cluster](media/adx-proxy/add-cluster.png)

    Se você adicionar uma conexão a mais de um cluster de proxy, dê a cada um deles um nome diferente. Caso contrário, eles terão o mesmo nome no painel esquerdo.

1. Depois que a conexão for estabelecida, seu cluster LA ou ia será exibido no painel esquerdo com seu cluster ADX nativo. 

    ![Log Analytics e clusters de Data Explorer do Azure](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Executar consultas

Você pode executar as consultas usando ferramentas de cliente que dão suporte a consultas Kusto, como: Kusto Explorer, interface do usuário da Web ADX, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, lentes, API REST.

> [!TIP]
> * O nome do banco de dados deve ter o mesmo nome que o recurso especificado no cluster de proxy. Os nomes diferenciam maiúsculas de minúsculas.
> * Em consultas entre clusters, certifique-se de que a nomenclatura de Application Insights aplicativos e espaços de trabalho do Log Analytics esteja correta.
>     * Se os nomes contiverem caracteres especiais, eles serão substituídos pela codificação de URL no nome do cluster de proxy. 
>     * Se os nomes incluírem caracteres que não atendem às [regras de nome de identificador KQL](/azure/kusto/query/schema-entities/entity-names), eles serão substituídos pelo caractere de **-** Dash.

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Consulta direta de seu cluster de proxy de LA ou ia ADX

Execute consultas em seu cluster LA ou ia. Verifique se o cluster está selecionado no painel esquerdo. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Consultar espaço de trabalho](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Consulta cruzada de seu cluster de proxy LA ou ia ADX e o cluster nativo ADX 

Ao executar consultas entre clusters do proxy, verifique se o cluster nativo do ADX está selecionado no painel esquerdo. Os exemplos a seguir demonstram a combinação de tabelas de cluster ADX (usando `union`) com a LA espaço de trabalho.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![consulta cruzada do proxy de Data Explorer do Azure](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

Usar o [operador de`join`](/azure/kusto/query/joinoperator), em vez de Union, pode exigir uma [`hint`](/azure/kusto/query/joinoperator#join-hints) para executá-lo em um cluster nativo do Azure data Explorer (e não no proxy). 

## <a name="additional-syntax-examples"></a>Exemplos de sintaxe adicionais

As seguintes opções de sintaxe estão disponíveis ao chamar os clusters de Application Insights (ia) ou Log Analytics (LA):

|Descrição da sintaxe  |Percepções sobre o Aplicativo  |Log Analytics  |
|----------------|---------|---------|
| Banco de dados dentro de um cluster que contém apenas o recurso definido nesta assinatura (**recomendado para consultas entre clusters**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster que contém todos os aplicativos/espaços de trabalho nesta assinatura    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster que contém todos os aplicativos/espaços de trabalho na assinatura e que são membros deste grupo de recursos    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster que contém apenas o recurso definido nesta assinatura      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Próximos passos

[Gravar consultas](write-queries.md)
