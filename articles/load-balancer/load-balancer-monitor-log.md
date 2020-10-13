---
title: Monitorar operações, eventos e contadores para o Load Balancer básico público
titleSuffix: Azure Load Balancer
description: Aprenda como habilitar eventos de alerta e analisar o log de status de integridade para o Public Basic Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 42ec5a661bd7b42ba5de5bfa99b3898291cc60fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935595"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Logs do Azure Monitor para o Basic Load Balancer

Você pode usar diferentes tipos de logs no Azure para gerenciar e solucionar problemas básicos de balanceadores de carga. Alguns desses logs podem ser acessados por meio do portal. Os logs podem ser transmitidos para um hub de eventos ou um espaço de trabalho Log Analytics. Todos os logs podem ser extraídos do armazenamento de BLOBs do Azure e exibidos em diferentes ferramentas, como Excel e Power BI.  Você pode saber mais sobre os diferentes tipos de logs na lista abaixo.

* **Logs de atividade:** Você pode usar [Exibir logs de atividades para monitorar ações em recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) para exibir todas as atividades que estão sendo enviadas para suas assinaturas do Azure e seu status. Os logs de atividade são habilitados por padrão e podem ser exibidos no portal do Azure.
* **Logs de eventos de alerta:** você pode usar esse log para ver quais alertas foram gerados para o balanceador de carga. O status do balanceador de carga é coletado a cada cinco minutos. Esse log será gravado somente se um evento de alerta do balanceador de carga for gerado.
* **Logs de investigação de integridade:** Você pode usar esse log para exibir os problemas detectados pelo seu teste de integridade, como o número de instâncias no pool de back-end que não estão recebendo solicitações do balanceador de carga devido a falhas de investigação de integridade. Esse log é gravado quando há uma alteração no status de investigação de integridade.

> [!IMPORTANT]
> **Os logs de eventos de investigação de integridade não estão funcionais no momento e estão listados nos [problemas conhecidos do Azure Load Balancer](whats-new.md#known-issues).** Os logs estão disponíveis apenas para os recursos implantados no modelo de implantação do Gerenciador de Recursos. Você não pode usar logs para recursos do modelo de implantação clássico. Para saber mais sobre esses modelos de implantação, consulte [Understanding Resource Manager deployment and classic deployment (Noções básicas sobre a implantação do Resource Manager e a implantação clássica)](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Habilitar o registro em log

O log de atividade é habilitado automaticamente para todos os recursos do Resource Manager. Habilite o log de investigação de integridade e de evento para começar a coletar os dados disponíveis por meio desses logs. Use as etapas a seguir para habilitar o registro em log.

Entre no [portal do Azure](https://portal.azure.com). Se você ainda não tiver um balanceador de carga, [crie um](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) antes de continuar.

1. No portal, clique em **grupos de recursos**.
2. Selecione **\<resource-group-name>** onde está o balanceador de carga.
3. Selecione seu balanceador de carga.
4. Selecione Configurações de diagnóstico do **log de atividades**  >  **Diagnostic settings**.
5. No painel **configurações de diagnóstico** , em **configurações de diagnóstico**, selecione **+ Adicionar configuração de diagnóstico**.
6. No painel criação de **configurações de diagnóstico** , insira **MyLBDiagnostics** no campo **nome** .
7. Você tem três opções para as **configurações de diagnóstico**.  Você pode escolher um, dois ou todos os três e configurar cada um para seus requisitos:
   * **Arquivar em uma conta de armazenamento**
   * **Transmitir por streaming para um hub de eventos**
   * **Enviar para o Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Arquivar em uma conta de armazenamento
    Você precisará de uma conta de armazenamento já criada para esse processo.  Para criar uma conta de armazenamento, consulte [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Marque a caixa de seleção ao lado de **arquivar em uma conta de armazenamento**.
    2. Selecione **Configurar** para abrir o painel **selecionar uma conta de armazenamento** .
    3. Selecione a **assinatura** na qual sua conta de armazenamento foi criada na caixa de pull.
    4. Selecione o nome da sua conta de armazenamento em **conta de armazenamento** na caixa de pull.
    5. Selecione OK.

    ### <a name="stream-to-an-event-hub"></a>Transmitir para um hub de eventos
    Você precisará de um hub de eventos já criado para esse processo.  Para criar um hub de eventos, consulte [início rápido: criar um hub de eventos usando portal do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. Marque a caixa de seleção ao lado de **transmitir para um hub de eventos**
    2. Selecione **Configurar** para abrir o painel **selecionar Hub de eventos** .
    3. Selecione a **assinatura** na qual o Hub de eventos foi criado na caixa de pull.
    4. **Selecione o namespace do hub de eventos** na caixa de pull.
    5. **Selecione nome da política do hub de eventos** na caixa de pull.
    6. Selecione OK.

    ### <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
    Você precisará já ter um espaço de trabalho do log Analytics criado e configurado para esse processo.  Para criar um espaço de trabalho Log Analytics, consulte [criar um espaço de trabalho log Analytics no portal do Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. Marque a caixa de seleção ao lado de **Enviar para log Analytics**.
    2. Selecione a **assinatura** na qual seu espaço de trabalho do log Analytics está na caixa de pull.
    3. Selecione o **espaço de trabalho log Analytics** na caixa de pull.


8. Abaixo da seção **log** no painel **configurações de diagnóstico** , marque a caixa de seleção ao lado de ambos:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Abaixo da seção **métrica** no painel **configurações de diagnóstico** , marque a caixa de seleção ao lado de:
   * **Biometria**

11. Verifique se tudo está correto e clique em **salvar** na parte superior do painel criar **configurações de diagnóstico** .

## <a name="activity-log"></a>Log de atividades

O log de atividades é gerado por padrão. Os logs são preservados por 90 dias no repositório de Logs de Eventos do Azure. Saiba mais sobre esses logs lendo o artigo [Exibir logs de atividades para monitorar ações em recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) .

## <a name="archive-to-storage-account-logs"></a>Arquivar em logs de conta de armazenamento

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

A saída JSON mostra a propriedade *EventName* , que descreverá o motivo para o balanceador de carga ter criado um alerta. Nesse caso, o alerta gerado foi devido ao esgotamento da porta TCP causado pelos limites de NAT do IP de origem (SNAT).

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

A saída JSON mostra no campo de propriedades as informações básicas do status da integridade da investigação. A propriedade *dipDownCount* mostra o número total de instâncias no back-end, que não estão recebendo tráfego de rede devido a respostas de investigação com falha.

### <a name="view-and-analyze-the-activity-log"></a>Exibir e analisar o log de atividades

Você pode exibir e analisar dados do log de atividades usando qualquer um dos seguintes métodos:

* **Ferramentas do Azure:** Recupere informações do log de atividades por meio do Azure PowerShell, a CLI (interface de linha de comando) do Azure, a API REST do Azure ou o portal do Azure. Instruções passo a passo para cada método são detalhadas no artigo [Operações de auditoria com o Gerenciador de Recursos](../azure-resource-manager/management/view-activity-logs.md) .
* **Power bi:** Se você ainda não tiver uma conta de [Power bi](https:// .microsoft.com/pricing) , poderá experimentá-la gratuitamente. Usando o [Pacote de conteúdo dos Logs de Auditoria do Azure para Power BI](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs), é possível analisar seus dados com painéis pré-configurados que ou é possível personalizar as exibições para elas se adequarem aos seus requisitos.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Exibir e analisar o log de eventos de investigação de integridade

Conecte-se à sua conta de armazenamento e recupere as entradas de log JSON para logs de investigação de eventos e de integridade. Depois de baixar os arquivos JSON, você pode convertê-los em CSV e exibi-los no Excel, Power BI ou em qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com o Visual Studio e os conceitos básicos de alteração de valores de constantes e variáveis em C#, você poderá usar as [ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.

## <a name="stream-to-an-event-hub"></a>Transmitir para um hub de eventos
Quando as informações de diagnóstico são transmitidas para um hub de eventos, elas podem ser usadas para análise de log centralizada em uma ferramenta SIEM de terceiros com integração de Azure Monitor. Para obter mais informações, consulte [transmitir dados de monitoramento do Azure para um hub de eventos](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
Os recursos no Azure podem ter suas informações de diagnóstico enviadas diretamente para um espaço de trabalho Log Analytics onde consultas complexas podem ser executadas em relação às informações para solução de problemas e análise.  Para obter mais informações, consulte [coletar logs de recursos do Azure no espaço de trabalho log Analytics no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>Próximas etapas

[Entenda as investigações do balanceador de carga](load-balancer-custom-probe-overview.md)
