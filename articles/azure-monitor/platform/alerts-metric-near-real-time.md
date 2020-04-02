---
title: Recursos com suporte para alertas de métrica no Azure Monitor
description: Referência sobre métricas e logs de suporte para alertas de métrica no Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: 62c1e29b668c3ef58b0e26e2749c55b913279425
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545688"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Recursos com suporte para alertas de métrica no Azure Monitor

Agora, o Azure Monitor dá suporte a um [novo tipo de alerta de métrica](../../azure-monitor/platform/alerts-overview.md), que tem vantagens significativas sobre os [alertas de métrica clássicos](../../azure-monitor/platform/alerts-classic.overview.md) mais antigos. As métricas estão disponíveis para uma [ampla lista de serviços do Azure](../../azure-monitor/platform/metrics-supported.md). Os alertas mais recentes dão suporte a um subconjunto (cada vez maior) de tipos de recurso. Este artigo lista esse subconjunto.

Você também pode usar alertas métricos mais novos em dados de log populares armazenados em um espaço de trabalho do Log Analytics extraídos como métricas. Para saber mais, exiba [Alertas de Métrica para Logs](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, suporte REST
Atualmente, você pode criar alertas métricos mais novos apenas no portal Azure, [API REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/)ou [Modelos de Gerenciador de Recursos](../../azure-monitor/platform/alerts-metric-create-templates.md). Suporte para configurar os alertas mais recentes usando o PowerShell e a CLI do Azure versões 2.0 e superiores em breve.

## <a name="metrics-and-dimensions-supported"></a>Medidas e dimensões com suporte
Os alertas de métrica mais recentes fornecem suporte a alertas para métricas que usam dimensões. É possível usar dimensões para filtrar sua métrica para o nível certo. Todas as métricas compatíveis, juntamente com as dimensões aplicáveis, podem ser exploradas e visualizadas no [Azure Monitor – Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

Esta é a lista completa das origens métricas do Azure Monitor com suporte pelos alertas mais recentes:

|Tipo de recurso  |Dimensões compatíveis |Alertas de vários recursos| Métricas disponíveis|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Sim| Não | [Gerenciamento de API](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.AppPlatform/Spring |Não| Sim|
|Microsoft.Automation/automationAccounts | Sim| Não | [Contas de Automação](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/D| Não | [Contas de Lote](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Sim| Não |[Cache Redis do Azure](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.ClassicArmazenamento/armazenamentoContas/mmxclassic|Não|Sim|
|Microsoft.ClassicArmazenamento/armazenamentoContas/mmxclassic/blobServices|Não|Sim|
|Microsoft.ClassicArmazenamento/armazenamentoContas/mmxclassic/fileServices|Não|Sim|
|Microsoft.ClassicArmazenamento/armazenamentoContas/mmxclassic/queueServices|Não|Sim|
|Microsoft.ClassicArmazenamento/armazenamentoContas/mmxclassic/tableServices|Não|Sim| |
|Microsoft.CognitiveServices/accounts| N/D | Não | [Serviços Cognitivos](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Sim | Sim | [Máquinas Virtuais](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |N/D | Sim |[conjuntos de escala de máquina virtual](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Sim| Não | [Grupos de contêineres](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Sim | Não | [Clusters gerenciados](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDispositivos | Sim | Sim | |
|Microsoft.DataFactory/datafactories| Sim| Não | [Data Factories V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories |Sim | Não |[Data Factories V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DataShare/contas |Não| Sim|
|Microsoft.DBforMySQL/servers |N/D| Não |[Banco de dados para MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |N/D | Não | [Banco de dados para PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | N/D | Não |[Métricas do Hub IoT](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Sim | Não |[Métricas DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/domínios|Não|Sim| |
|Microsoft.EventGrid/topics |Sim | Não |[Tópicos da grade de eventos](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |Sim| Não |[Clusters de hubs de eventos](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |Sim| Não |[Hubs de Eventos](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Não |Não |[Cofres](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |N/D | Não |[Aplicativos Lógicos](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|Sim| Não | [Aprendizado de Máquina](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |Sim| Não | [Pools de capacidade do Azure NetApp](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppContas/capacityPools/volumes |Sim| Não | [Azure NetApp Volumes](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|N/D| Não |  |
|Microsoft.Network/dnsZones | N/D| Não | [Zonas de DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N/D | Não |[Circuitos do ExpressRoute](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (apenas para SKUs Standard)| Sim| Não | [Balanceadores de carga](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways|Não|Sim|
|Microsoft.Network/privateEndpoints|Não|Sim|
|Microsoft.Network/privateLinkServices|Não|Sim|
|Microsoft.Network/publicipaddresses |N/D | Não |[Endereços IP Públicos](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Sim | Não | [Perfis do Gerenciador de Tráfego](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Sim | Não | [Workspaces do Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/namespaces | Sim | Não | [Retransmissão](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft.Peering/peeringServices|Não|Sim|
|Microsoft.PowerBIDedicated/capacities | N/D | Não | [Capacidades](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |N/D|Não | [Serviços Search](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |Sim| Não |[Barramento de Serviço](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |    Não | Sim |
|Microsoft.Sql/servers/databases    | Não | Sim |
|Microsoft.Storage/storageAccounts |Sim | Não | [Contas de armazenamento](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Sim| Não | [Serviços de blob](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Serviços de arquivo](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [Serviços de fila](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) e [Serviços de tabela](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |N/D| Não | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.Microsoft.VMWareCloudMáquinas simples/virtuais |Sim|Não |[Máquinas Virtuais de CloudSimple](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingEnvironments/multiRolePools | Sim | Não | [Ambiente de serviço de aplicativos Pools multi-funções](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Sim | Não | [Pools de trabalhadores do ambiente de serviço do aplicativo](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Sim | Não | [Planos de serviço de aplicativos](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Sim | Não | [Serviços de Aplicativos](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites)|
|Microsoft.Web/sites/slots | Sim | Não | [Slots do Serviço de Aplicativo](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Esquema de conteúdo

> [!NOTE]
> Você também pode usar o [esquema de alerta comum,](https://aka.ms/commonAlertSchemaDocs)que oferece a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta no Azure Monitor, para suas integrações de webhook. [Conheça as definições comuns do esquema de alerta.](https://aka.ms/commonAlertSchemaDefinitions)


A operação POST contém a carga JSON e esquema a seguir para todos os alertas de métrica mais recentes quando o [grupo de ações ](../../azure-monitor/platform/action-groups.md) adequadamente configurado é usado:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a nova [Experiência de alertas](../../azure-monitor/platform/alerts-overview.md).
* Saiba mais sobre os [alertas de log no Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Saiba mais sobre [alertas no Azure](../../azure-monitor/platform/alerts-overview.md).
