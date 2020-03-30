---
title: Dados de consulta no Monitor Azure com o Azure Data Explorer (Preview)
description: Neste tópico, consulte dados no Azure Monitor criando um proxy do Azure Data Explorer para consultas cruzadas de produtos com insights de aplicativos e análises de log
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: c7e98c31c0db1db3051ad66df6526dcbddb265c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560415"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Dados de consulta no Azure Monitor usando o Azure Data Explorer (Preview)

O azure Data Explorer proxy cluster (ADX Proxy) é uma entidade que permite que você execute consultas cruzadas de produtos entre o Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)e [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) no serviço [Azure Monitor.](/azure/azure-monitor/) Você pode mapear os espaços de trabalho do Azure Monitor Log Analytics ou os aplicativos Application Insights como clusters proxy. Em seguida, você pode consultar o cluster proxy usando as ferramentas do Azure Data Explorer e encaminhá-lo em uma consulta de cluster cruzado. O artigo mostra como se conectar a um cluster proxy, adicionar um cluster proxy à UI do Azure Data Explorer e executar consultas contra seus aplicativos de IA ou espaços de trabalho LA do Azure Data Explorer.

O fluxo proxy do Azure Data Explorer: 

![Fluxo proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> O Proxy ADX está no modo de visualização. [Conecte-se ao proxy](#connect-to-the-proxy) para habilitar o recurso proxy ADX para seus clusters. Entre em contato com a equipe do [ADXProxy](mailto:adxproxy@microsoft.com) com quaisquer dúvidas.

## <a name="connect-to-the-proxy"></a>Conecte-se ao proxy

1. Verifique se o cluster nativo do Azure Data Explorer (como cluster *de ajuda)* aparece no menu esquerdo antes de se conectar ao cluster Log Analytics ou Application Insights.

    ![Cluster nativo do ADX](media/adx-proxy/web-ui-help-cluster.png)

1. Na UI do Azurehttps://dataexplorer.azure.com/clusters)Data Explorer (, selecione **Adicionar cluster**).

1. Na **janela Adicionar cluster,** adicione a URL ao cluster LA ou AI. 
    
    * Para LA:`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Para IA:`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Selecione **Adicionar**.

    ![Adicionar cluster](media/adx-proxy/add-cluster.png)

    Se você adicionar uma conexão a mais de um cluster proxy, dê a cada um um nome diferente. Caso contrário, todos eles terão o mesmo nome no painel esquerdo.

1. Depois que a conexão for estabelecida, seu cluster LA ou IA aparecerá no painel esquerdo com o cluster ADX nativo. 

    ![Clusters log analytics e azure data explorer](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Executar consultas

Você pode executar as consultas usando ferramentas cliente que suportam consultas kusto, tais como: Kusto Explorer, ADX Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, REST API.

> [!TIP]
> * O nome do banco de dados deve ter o mesmo nome do recurso especificado no cluster proxy. Nomes são sensíveis a casos.
> * Em consultas de cluster cruzadas, certifique-se de que a nomenclatura de aplicativos do Application Insights e espaços de trabalho do Log Analytics esteja correta.
>     * Se os nomes contiverem caracteres especiais, eles são substituídos por codificação de URL no nome do cluster proxy. 
>     * Se os nomes incluem caracteres que não atendem às regras de **-** nome do [identificador KQL,](/azure/kusto/query/schema-entities/entity-names)eles serão substituídos pelo caractere dash.

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Consulta direta do cluster Proxy LA ou AI ADX

Execute consultas em seu cluster LA ou IA. Verifique se o cluster está selecionado no painel esquerdo. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Espaço de trabalho de Consulta LA](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Consulta cruzada do cluster Proxy LA ou AI ADX e do cluster nativo do ADX 

Quando você executar consultas de cluster cruzado do proxy, verifique se o cluster nativo do ADX está selecionado no painel esquerdo. Os exemplos a seguir demonstram a combinação `union`de tabelas de cluster ADX (usando) com o espaço de trabalho la.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![Consulta cruzada do proxy do Azure Data Explorer](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

O [ `join` ](/azure/kusto/query/joinoperator)uso do operador , em [`hint`](/azure/kusto/query/joinoperator#join-hints) vez de união, pode exigir um para executá-lo em um cluster nativo do Azure Data Explorer (e não no proxy). 

## <a name="additional-syntax-examples"></a>Exemplos adicionais de sintaxe

As seguintes opções de sintaxe estão disponíveis ao ligar para os clusters Application Insights (AI) ou Log Analytics (LA):

|Descrição da sintaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Banco de dados dentro de um cluster que contém apenas o recurso definido nesta assinatura **(recomendado para consultas de cluster cruzado)** |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster que contém todos os aplicativos/espaços de trabalho nesta assinatura    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster que contém todos os aplicativos/espaços de trabalho na assinatura e são membros deste grupo de recursos    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster que contém apenas o recurso definido nesta assinatura      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Próximas etapas

[Gravar consultas](write-queries.md)
