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
ms.openlocfilehash: 0a21af683d9fa7849d3e96c545983c9f40a8d4c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935329"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Logs do Azure Monitor para o Basic Load Balancer

>[!IMPORTANT]
>O Azure Load Balancer oferece suporte a dois tipos diferentes: Basic e Standard. Este artigo discute o Load Balancer Basic. Para obter mais informações sobre o Load Balancer Standard, consulte [visão geral do balanceador de carga padrão](load-balancer-standard-overview.md) que expõe a telemetria por meio de métricas multidimensionais no Azure Monitor.

Você pode usar diferentes tipos de logs no Azure para gerenciar e solucionar problemas básicos de balanceadores de carga. Alguns desses logs podem ser acessados por meio do portal. Os logs podem ser transmitidos para um hub de eventos ou um espaço de trabalho do Log Analytics. Todos os logs podem ser extraídos do armazenamento blob do Azure e visualizados em diferentes ferramentas, como Excel e Power BI.  Você pode saber mais sobre os diferentes tipos de logs na lista abaixo.

* **Registros de atividades:** Você pode usar [os registros de atividades do Exibir para monitorar ações nos recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) para visualizar todas as atividades que estão sendo submetidas à sua assinatura do Azure e seu status. Os registros de atividades são ativados por padrão e podem ser visualizados no portal Azure.
* **Logs de eventos de alerta:** você pode usar esse log para ver quais alertas foram gerados para o balanceador de carga. O status do balanceador de carga é coletado a cada cinco minutos. Esse log será gravado somente se um evento de alerta do balanceador de carga for gerado.
* **Logs de investigação de integridade:** Você pode usar esse log para exibir os problemas detectados pelo seu teste de integridade, como o número de instâncias no pool de back-end que não estão recebendo solicitações do balanceador de carga devido a falhas de investigação de integridade. Esse log é gravado quando há uma alteração no status de investigação de integridade.

> [!IMPORTANT]
> Atualmente, os logs do Monitor Do Azure funcionam apenas para balanceadores de carga básicos públicos. Os logs estão disponíveis apenas para os recursos implantados no modelo de implantação do Gerenciador de Recursos. Você não pode usar logs para recursos do modelo de implantação clássico. Para saber mais sobre esses modelos de implantação, consulte [Understanding Resource Manager deployment and classic deployment (Noções básicas sobre a implantação do Resource Manager e a implantação clássica)](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Habilitar o registro em log

O log de atividade é habilitado automaticamente para todos os recursos do Resource Manager. Habilite o registro de eventos e testes de saúde para começar a coletar os dados disponíveis através desses registros. Use as etapas a seguir para habilitar o registro em log.

Faça login no [portal Azure](https://portal.azure.com). Se você ainda não tiver um balanceador de carga, [crie um](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) antes de continuar.

1. No portal, clique **em grupos de recursos**.
2. Selecione o ** \<nome do grupo de recursos>** onde está o balanceador de carga.
3. Selecione o balanceador de carga.
4. Selecione As**configurações de diagnóstico de** **monitoramento** > .
5. No painel **de configurações Diagnósticos,** em **Configurações de Diagnóstico,** selecione **+ Adicionar configuração de diagnóstico**.
6. No painel de criação **de configurações Diagnósticos,** digite **myLBDiagnostics** no campo **Nome.**
7. Você tem três opções para as **configurações diagnósticos**.  Você pode escolher um, dois ou todos os três e configurar cada um para seus requisitos:
   * **Arquivar em uma conta de armazenamento**
   * **Fluxo para um hub de eventos**
   * **Enviar para o Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Arquivar em uma conta de armazenamento
    Você precisará de uma conta de armazenamento já criada para este processo.  Para criar uma conta de armazenamento, consulte [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Selecione a caixa de seleção ao lado **de Archive para uma conta de armazenamento**.
    2. Selecione **Configurar** para abrir o painel Selecionar uma conta de **armazenamento.**
    3. Selecione a **Assinatura** onde sua conta de armazenamento foi criada na caixa pull-down.
    4. Selecione o nome da sua conta de armazenamento na **conta armazenamento** na caixa pull-down.
    5. Selecione OK.

    ### <a name="stream-to-an-event-hub"></a>Transmitir para um hub de eventos
    Você precisará de um hub de eventos já criado para esse processo.  Para criar um hub de eventos, consulte [Quickstart: Crie um hub de eventos usando o portal Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. Selecione a caixa de seleção ao lado **de Stream para um hub de eventos**
    2. Selecione **Configurar** para abrir o painel **de eventos Select.**
    3. Selecione a **Assinatura** onde seu hub de eventos foi criado na caixa pull-down.
    4. **Selecione o namespace do hub de eventos** na caixa pull-down.
    5. **Selecione o nome da diretiva** do hub de eventos na caixa pull-down.
    6. Selecione OK.

    ### <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
    Você precisará já ter um espaço de trabalho de análise de log criado e configurado para este processo.  Para criar um espaço de trabalho do Log Analytics, consulte [Criar um espaço de trabalho do Log Analytics no portal Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. Selecione a caixa de seleção ao lado **de Enviar para Log Analytics**.
    2. Selecione a **Assinatura** onde o espaço de trabalho do Log Analytics está na caixa pull-down.
    3. Selecione o **Espaço de trabalho do Log Analytics** na caixa pull-down.


8. Abaixo da seção **LOG** no painel **Configurações de Diagnóstico,** selecione a caixa de seleção ao lado de ambos:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Abaixo da seção **MÉTRICA** no painel **de configurações Diagnósticos,** selecione a caixa de seleção ao lado de:
   * **AllMetrics**

11. Verifique se tudo parece correto e clique em **Salvar** na parte superior do painel **criar configurações de diagnóstico.**

## <a name="activity-log"></a>Log de atividades

O registro de atividades é gerado por padrão. Os logs são preservados por 90 dias no repositório de Logs de Eventos do Azure. Saiba mais sobre esses logs lendo os registros de atividade do Exibir para monitorar ações no artigo [de recursos.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)

## <a name="archive-to-storage-account-logs"></a>Arquivar para registros de contas de armazenamento

### <a name="alert-event-log"></a>Log de eventos de alerta

Esse log só será gerado se você o tiver habilitado para cada balanceador de carga. Os dados são registrados no formato JSON e armazenados na conta de armazenamento especificada quando você habilitou o registro em log. O exemplo a seguir é de um evento.

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

A saída JSON mostra a propriedade *eventname,* que descreverá a razão para o balanceador de carga criar um alerta. Neste caso, o alerta gerado foi devido à exaustão da porta TCP causada pelos limites de NAT IP de origem (SNAT).

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

A saída JSON mostra no campo de propriedades as informações básicas do status da integridade da investigação. A propriedade *dipDownCount* mostra o número total de instâncias no back-end, que não estão recebendo tráfego de rede devido a respostas de teste com falha.

### <a name="view-and-analyze-the-activity-log"></a>Exibir e analisar o log de atividades

Você pode exibir e analisar dados do log de atividades usando qualquer um dos seguintes métodos:

* **Ferramentas azure:** Recupere informações do registro de atividades através do Azure PowerShell, da Interface de Linha de Comando (CLI) do Azure, da API Do Azure REST ou do portal Azure. Instruções passo a passo para cada método são detalhadas no artigo [Operações de auditoria com o Gerenciador de Recursos](../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI:** Se você ainda não tem uma conta [de Power BI,](https:// .microsoft.com/pricing) você pode experimentá-la gratuitamente. Usando o [Pacote de conteúdo dos Logs de Auditoria do Azure para Power BI](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs), é possível analisar seus dados com painéis pré-configurados que ou é possível personalizar as exibições para elas se adequarem aos seus requisitos.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Exibir e analisar o log de eventos de investigação de integridade

Conecte-se à sua conta de armazenamento e recupere as entradas de registro JSON para registros de eventos e testes de saúde. Depois de baixar os arquivos JSON, você pode convertê-los em CSV e visualizá-los no Excel, Power BI ou qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com o Visual Studio e os conceitos básicos de alteração de valores de constantes e variáveis em C#, você poderá usar as [ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.

## <a name="stream-to-an-event-hub"></a>Transmitir para um hub de eventos
Quando as informações de diagnóstico são transmitidas para um hub de eventos, elas podem ser usadas para análise centralizada de log em uma ferramenta SIEM de terceiros com integração do Monitor Azure. Para obter mais informações, consulte [os dados de monitoramento do Stream Azure em um hub de eventos](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
Os recursos no Azure podem ter suas informações de diagnóstico enviadas diretamente para um espaço de trabalho do Log Analytics, onde consultas complexas podem ser executadas contra as informações para solução e análise de problemas.  Para obter mais informações, consulte [os logs de recursos do Collect Azure no espaço de trabalho do Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>Próximas etapas

[Entenda as investigações do balanceador de carga](load-balancer-custom-probe-overview.md)
