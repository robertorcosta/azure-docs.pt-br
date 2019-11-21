---
title: Monitorar operações, eventos e contadores para o Load Balancer básico público
titleSuffix: Azure Load Balancer
description: Aprenda como habilitar eventos de alerta e analisar o log de status de integridade para o Public Basic Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: allensu
ms.openlocfilehash: 23a3a2629c6f2f89c4b8f6d5af57bcf3b6bb67dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214923"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Azure Monitor logs for public Basic Load Balancer

>[!IMPORTANT] 
>O Azure Load Balancer oferece suporte a dois tipos diferentes: Basic e Standard. Este artigo discute o Load Balancer Basic. Para obter mais informações sobre o Load Balancer Standard, consulte [visão geral do balanceador de carga padrão](load-balancer-standard-overview.md) que expõe a telemetria por meio de métricas multidimensionais no Azure Monitor.

Você pode usar diferentes tipos de logs no Azure para gerenciar e solucionar problemas básicos de balanceadores de carga. Alguns desses logs podem ser acessados por meio do portal. Logs can be streamed to an event hub or a Log Analytics workspace. All logs can be extracted from Azure blob storage, and viewed in different tools, such as Excel and Power BI.  Você pode saber mais sobre os diferentes tipos de logs na lista abaixo.

* **Activity logs:** You can use [View activity logs to monitor actions on resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) to view all activity being submitted to your Azure subscription(s), and their status. Activity logs are enabled by default, and can be viewed in the Azure portal.
* **Logs de eventos de alerta:** você pode usar esse log para ver quais alertas foram gerados para o balanceador de carga. O status do balanceador de carga é coletado a cada cinco minutos. Esse log será gravado somente se um evento de alerta do balanceador de carga for gerado.
* **Logs de investigação de integridade:** Você pode usar esse log para exibir os problemas detectados pelo seu teste de integridade, como o número de instâncias no pool de back-end que não estão recebendo solicitações do balanceador de carga devido a falhas de investigação de integridade. Esse log é gravado quando há uma alteração no status de investigação de integridade.

> [!IMPORTANT]
> Azure Monitor logs currently works only for public Basic load balancers. Os logs estão disponíveis apenas para os recursos implantados no modelo de implantação do Gerenciador de Recursos. Você não pode usar logs para recursos do modelo de implantação clássico. Para saber mais sobre esses modelos de implantação, consulte [Understanding Resource Manager deployment and classic deployment (Noções básicas sobre a implantação do Resource Manager e a implantação clássica)](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Habilitar o registro em log

O log de atividade é habilitado automaticamente para todos os recursos do Resource Manager. Enable event and health probe logging to start collecting the data available through those logs. Use as etapas a seguir para habilitar o registro em log.

Entre no [portal do Azure](https://portal.azure.com). Se você ainda não tiver um balanceador de carga, [crie um](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) antes de continuar.

1. In the portal, click **Resource groups**.
2. Select **\<resource-group-name>** where your load balancer is.
3. Select your load balancer.
4. Select **Monitoring** > **Diagnostic settings**.
5. In the **Diagnostics settings** pane, under **Diagnostics settings**, select **+ Add diagnostic setting**.
6. In the **Diagnostics settings** creation pane, enter **myLBDiagnostics** in the **Name** field.
7. You have three options for the **Diagnostics settings**.  You can choose one, two or all three and configure each for your requirements:
   * **Archive to a storage account**
   * **Stream to an event hub**
   * **Send to Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Arquivar em uma conta de armazenamento
    You'll need a storage account already created for this process.  To create a storage account, see [Create a storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Select the checkbox next to **Archive to a storage account**.
    2. Select **Configure** to open the **Select a storage account** pane.
    3. Select the **Subscription** where your storage account was created in the pull-down box.
    4. Select the name of your storage account under **Storage account** in the pull-down box. 
    5. Select OK.

    ### <a name="stream-to-an-event-hub"></a>Transmitir para um hub de eventos
    You'll need an event hub already created for this process.  To create an event hub, see [Quickstart: Create an event hub using Azure portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. Select the checkbox next to **Stream to an event hub**
    2. Select **Configure** to open the **Select event hub** pane.
    3. Select the **Subscription** where your event hub was created in the pull-down box.
    4. **Select event hub namespace** in the pull-down box.
    5. **Select event hub policy name** in the pull-down box.
    6. Select OK.

    ### <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
    You'll need to already have a log analytics workspace created and configured for this process.  To create a Log Analytics workspace, see [Create a Log Analytics workspace in the Azure portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. Select the checkbox next to **Send to Log Analytics**.
    2. Select the **Subscription** where your Log Analytics workspace is in the pull-down box.
    3. Select the **Log Analytics Workspace** in the pull-down box.


8. Beneath the **LOG** section in the **Diagnostics settings** pane, select the check box next to both:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Beneath the **METRIC** section in the **Diagnostics settings** pane, select the check box next to:
   * **AllMetrics**

11. Verify everything looks correct and click **Save** at the top of the create **Diagnostic settings** pane.

## <a name="activity-log"></a>Logs de atividades

The activity log is generated by default. Os logs são preservados por 90 dias no repositório de Logs de Eventos do Azure. Learn more about these logs by reading the [View activity logs to monitor actions on resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) article.

## <a name="archive-to-storage-account-logs"></a>Archive to storage account logs

### <a name="alert-event-log"></a>Log de eventos de alerta

Esse log só será gerado se você o tiver habilitado para cada balanceador de carga. Os dados são registrados no formato JSON e armazenados na conta de armazenamento especificada quando você habilitou o registro em log. The following example is of an event.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

The JSON output shows the *eventname* property, which will describe the reason for the load balancer created an alert. In this case, the alert generated was because of TCP port exhaustion caused by source IP NAT limits (SNAT).

### <a name="health-probe-log"></a>Log de investigação de integridade

Esse log só será gerado se você o tiver habilitado para cada balanceador de carga, conforme detalhado acima. Os dados são armazenados na conta de armazenamento especificada quando você habilitou o registro em log. Um contêiner denominado 'insights-logs-loadbalancerprobehealthstatus' é criado e os seguintes dados são registrados:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

A saída JSON mostra no campo de propriedades as informações básicas do status da integridade da investigação. The *dipDownCount* property shows the total number of instances on the back-end, which are not receiving network traffic because of failed probe responses.

### <a name="view-and-analyze-the-audit-log"></a>Exibir e analisar o log de auditoria

Você pode exibir e analisar dados do log de auditoria usando qualquer um dos seguintes métodos:

* **Azure tools:** Retrieve information from the audit logs through Azure PowerShell, the Azure Command Line Interface (CLI), the Azure REST API, or the Azure portal. Instruções passo a passo para cada método são detalhadas no artigo [Operações de auditoria com o Gerenciador de Recursos](../azure-resource-manager/resource-group-audit.md) .
* **Power BI:** se ainda não tiver uma conta do [Power BI](https:// .microsoft.com/pricing) , você poderá testá-lo gratuitamente. Usando o [Pacote de conteúdo dos Logs de Auditoria do Azure para Power BI](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs), é possível analisar seus dados com painéis pré-configurados que ou é possível personalizar as exibições para elas se adequarem aos seus requisitos.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Exibir e analisar o log de eventos de investigação de integridade

Connect to your storage account and retrieve the JSON log entries for event and health probe logs. Once you download the JSON files, you can convert them to CSV and view in Excel, Power BI, or any other data visualization tool.

> [!TIP]
> Se estiver familiarizado com o Visual Studio e os conceitos básicos de alteração de valores de constantes e variáveis em C#, você poderá usar as [ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.

## <a name="stream-to-an-event-hub"></a>Transmitir para um hub de eventos
When diagnostic information is streamed to an event hub, it can be used for centralized log analysis in a third-party SIEM tool with Azure Monitor Integration. For more information, see [Stream Azure monitoring data to an event hub](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
Resources in Azure can have their diagnostic information sent directly to a Log Analytics workspace where complex queries can be run against the information for troubleshooting and analysis.  For more information, see [Collect Azure resource logs in Log Analytics workspace in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>Próximos passos

[Entender as investigações do balanceador de carga](load-balancer-custom-probe-overview.md)
