---
title: Consulta entre recursos Data Explorer do Azure usando Azure Monitor
description: Use Azure Monitor para executar consultas entre produtos entre o Azure Data Explorer, espaços de trabalho Log Analytics e aplicativos Application Insights clássicos no Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 5cb2f7b3b07c20e09d61e97412bc35f03b15cb3b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572143"
---
# <a name="cross-resource-query-azure-data-explorer-using-azure-monitor"></a>Consulta entre recursos Data Explorer do Azure usando Azure Monitor
Azure Monitor dá suporte a consultas entre serviços entre o Azure Data Explorer, [Application insights (ia)](/azure/azure-monitor/app/app-insights-overview)e [log Analytics (la)](/azure/azure-monitor/platform/data-platform-logs). Você pode consultar o cluster de Data Explorer do Azure usando ferramentas de Log Analytics/Application Insights e consultá-lo em uma consulta entre serviços. O artigo mostra como fazer uma consulta entre serviços.

O Azure Monitor fluxo entre serviços: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Azure monitor e fluxo de serviço de data Explorer do Azure.":::

>[!NOTE]
>* Azure Monitor consulta entre serviços está em visualização privada-AllowListing é necessário.
>* Entre em contato com a [equipe de serviço](mailto:ADXProxy@microsoft.com) com perguntas.
## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Entre em consulta cruzada seus recursos de Log Analytics ou Application Insights e o Azure Data Explorer

Você pode executar as consultas de recurso cruzado usando ferramentas de cliente que dão suporte a consultas Kusto, como: Log Analytics interface do usuário da Web, pastas de trabalho, PowerShell, API REST e muito mais.

* Insira o identificador para um cluster de Data Explorer do Azure em uma consulta dentro do padrão ' ADX ' seguido pelo nome do banco de dados e pela tabela.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Exemplo de consulta de serviço cruzado.":::

> [!NOTE]
>* Nomes de bancos de dados diferenciam maiúsculas de minúsculas
>* Não há suporte para a consulta entre recursos como um alerta.
## <a name="combining-azure-data-explorer-cluster-tables-using-union-and-join-with-la-workspace"></a>Combinando tabelas de cluster Data Explorer do Azure (usando Union e Join) com a LA espaço de trabalho.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Exemplo de consulta entre serviços com Union.":::

>[!Tip]
>* O formato abreviado é permitido-ClusterName/InitialCatalog. Por exemplo, ADX (' Help/samples ') é convertido em ADX (' Help. Kusto. Windows. net/samples ')
## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Unir dados de um cluster de Data Explorer do Azure em um locatário com um recurso de Azure Monitor em outro

Não há suporte para consultas entre locatários entre os serviços. Você está conectado a um único locatário para executar a consulta abrangendo ambos os recursos.

Se o recurso de Data Explorer do Azure estiver no locatário ' A ' e Log Analytics espaço de trabalho estiver no locatário ' B ', use um dos dois métodos a seguir:

*  O Data Explorer do Azure permite que você adicione funções para entidades de segurança em locatários diferentes. Adicione sua ID de usuário no locatário ' B ' como um usuário autorizado no cluster de Data Explorer do Azure. Valide a propriedade *[' TrustedExternalTenant '](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* no cluster de data Explorer do Azure que contém o locatário ' B '. Execute a consulta cruzada totalmente no locatário ' B '.
*  Use [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) para projetar o recurso Azure monitor no locatário ' A '.
## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Conectar-se ao Azure Data Explorer clusters de diferentes locatários

O Kusto Explorer entra automaticamente no locatário ao qual a conta de usuário pertence originalmente. Para acessar recursos em outros locatários com a mesma conta de usuário, o `tenantId` deve ser especificado explicitamente na cadeia de conexão: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **tenantid**

## <a name="next-steps"></a>Próximas etapas
* [Gravar consultas](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Consultar dados em Azure Monitor usando o Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Executar consultas entre logs de recursos no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)
