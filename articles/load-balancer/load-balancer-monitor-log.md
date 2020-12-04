---
title: Monitorar operações, eventos e contadores para o Load Balancer básico público
titleSuffix: Azure Load Balancer
description: Saiba como habilitar o registro em log para o Azure Load Balancer
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
ms.openlocfilehash: 6742723e24df83ac8112e224f1999f116ab82c94
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572772"
---
# <a name="azure-monitor-logs-for-the-standard-azure-load-balancer"></a>Logs de Azure Monitor para o Azure Load Balancer padrão

Você pode usar diferentes tipos de logs no Azure para gerenciar e solucionar problemas de balanceadores de carga padrão. Os logs podem ser transmitidos para um hub de eventos ou um espaço de trabalho Log Analytics. Todos os logs podem ser extraídos do armazenamento de BLOBs do Azure e exibidos em diferentes ferramentas, como Excel e Power BI.  Você pode saber mais sobre os diferentes tipos de logs na lista abaixo.

* **Logs de atividade:** Você pode usar [Exibir logs de atividades para monitorar ações em recursos](../azure-resource-manager/management/view-activity-logs.md) para exibir todas as atividades que estão sendo enviadas para suas assinaturas do Azure e seu status. Os logs de atividade são habilitados por padrão e podem ser exibidos no portal do Azure. Esses logs estão disponíveis para os balanceadores de carga básico e Standard.
* **Métricas de Standard Load Balancer:** Você pode usar esse log para consultar as métricas exportadas como logs para o Azure Load Balancer padrão. Esses logs só estão disponíveis para balanceadores de carga padrão.

> [!IMPORTANT]
> **Os logs de eventos de alertas de investigação de integridade e Load Balancer não estão funcionais no momento e estão listados nos [problemas conhecidos do Azure Load Balancer](whats-new.md#known-issues).** 

> [!IMPORTANT]
> Os logs estão disponíveis apenas para os recursos implantados no modelo de implantação do Gerenciador de Recursos. Você não pode usar logs para recursos do modelo de implantação clássico. Para saber mais sobre esses modelos de implantação, consulte [Understanding Resource Manager deployment and classic deployment (Noções básicas sobre a implantação do Resource Manager e a implantação clássica)](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Habilitar o registro em log

O log de atividade é habilitado automaticamente para todos os recursos do Resource Manager. Habilite o log de investigação de integridade e de evento para começar a coletar os dados disponíveis por meio desses logs. Use as etapas a seguir para habilitar o registro em log.

Entre no [portal do Azure](https://portal.azure.com). Se você ainda não tiver um balanceador de carga, [crie um](./quickstart-load-balancer-standard-public-portal.md) antes de continuar.

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
    Você precisará de uma conta de armazenamento já criada para esse processo.  Para criar uma conta de armazenamento, consulte [criar uma conta de armazenamento](../storage/common/storage-account-create.md?tabs=azure-portal)

    1. Marque a caixa de seleção ao lado de **arquivar em uma conta de armazenamento**.
    2. Selecione **Configurar** para abrir o painel **selecionar uma conta de armazenamento** .
    3. Selecione a **assinatura** na qual sua conta de armazenamento foi criada na caixa de pull.
    4. Selecione o nome da sua conta de armazenamento em **conta de armazenamento** na caixa de pull.
    5. Selecione OK.

    ### <a name="stream-to-an-event-hub"></a>Transmitir para um hub de eventos
    Você precisará de um hub de eventos já criado para esse processo.  Para criar um hub de eventos, consulte [início rápido: criar um hub de eventos usando portal do Azure](../event-hubs/event-hubs-create.md)

    1. Marque a caixa de seleção ao lado de **transmitir para um hub de eventos**
    2. Selecione **Configurar** para abrir o painel **selecionar Hub de eventos** .
    3. Selecione a **assinatura** na qual o Hub de eventos foi criado na caixa de pull.
    4. **Selecione o namespace do hub de eventos** na caixa de pull.
    5. **Selecione nome da política do hub de eventos** na caixa de pull.
    6. Selecione OK.

    ### <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
    Você precisará já ter um espaço de trabalho do log Analytics criado e configurado para esse processo.  Para criar um espaço de trabalho Log Analytics, consulte [criar um espaço de trabalho log Analytics no portal do Azure](../azure-monitor/learn/quick-create-workspace.md)

    1. Marque a caixa de seleção ao lado de **Enviar para log Analytics**.
    2. Selecione a **assinatura** na qual seu espaço de trabalho do log Analytics está na caixa de pull.
    3. Selecione o **espaço de trabalho log Analytics** na caixa de pull.


8.  Abaixo da seção **métrica** no painel **configurações de diagnóstico** , marque a caixa de seleção ao lado de: **biométricas**

9. Verifique se tudo está correto e clique em **salvar** na parte superior do painel criar **configurações de diagnóstico** .

## <a name="activity-log"></a>Log de atividades

O log de atividades é gerado por padrão. Ele pode ser configurado para ser exportado em um nível de assinatura [seguindo as instruções neste artigo](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log). Saiba mais sobre esses logs lendo o artigo [Exibir logs de atividades para monitorar ações em recursos](../azure-resource-manager/management/view-activity-logs.md) .

### <a name="view-and-analyze-the-activity-log"></a>Exibir e analisar o log de atividades

Você pode exibir e analisar dados do log de atividades usando qualquer um dos seguintes métodos:

* **Ferramentas do Azure:** Recupere informações do log de atividades por meio do Azure PowerShell, a CLI (interface de linha de comando) do Azure, a API REST do Azure ou o portal do Azure. Instruções passo a passo para cada método são detalhadas no artigo [Operações de auditoria com o Gerenciador de Recursos](../azure-resource-manager/management/view-activity-logs.md) .
* **Power bi:** Se você ainda não tiver uma conta de [Power bi](https://powerbi.microsoft.com/pricing) , poderá experimentá-la gratuitamente. Usando a [integração dos logs de auditoria do Azure para Power bi](https://powerbi.microsoft.com/integrations/azure-audit-logs/), você pode analisar seus dados com painéis pré-configurados ou pode personalizar modos de exibição para atender às suas necessidades.

## <a name="metrics-as-logs"></a>Métricas como logs
Usando métricas para logs funcionalidade de exportação fornecida por Azure Monitor, você pode exportar suas métricas de Load Balancer. Essas métricas irão gerar uma entrada de log para cada intervalo de amostragem de um minuto.

A métrica para a exportação de log está habilitada em um nível por recurso. Você pode habilitar esses logs acessando a folha configurações de diagnóstico, filtrando por grupo de recursos e selecionando o Load Balancer para o qual você gostaria de habilitar a exportação de métricas. Quando a página Load Balancer configurações de diagnóstico estiver ativa, selecione biométricas para exportar métricas qualificadas como logs.

Consulte a seção [limitações](#limitations) deste artigo para obter limitações de exportação de métrica.

### <a name="view-and-analyze-metrics-as-logs"></a>Exibir e analisar métricas como logs
Depois de habilitar as biométricas nas configurações de diagnóstico de seu Standard Load Balancer, se estiver usando um hub de eventos ou Log Analytics espaço de trabalho, esses logs serão preenchidos na tabela AzureMonitor. Se estiver exportando para o armazenamento, conecte-se à sua conta de armazenamento e recupere as entradas de log JSON para logs de investigação de eventos e de integridade. Depois de baixar os arquivos JSON, você pode convertê-los em CSV e exibi-los no Excel, Power BI ou em qualquer outra ferramenta de visualização de dados. 

> [!TIP]
> Se estiver familiarizado com o Visual Studio e os conceitos básicos de alteração de valores de constantes e variáveis em C#, você poderá usar as [ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.

## <a name="stream-to-an-event-hub"></a>Transmitir para um hub de eventos
Quando as informações de diagnóstico são transmitidas para um hub de eventos, elas podem ser usadas para análise de log centralizada em uma ferramenta SIEM de terceiros com integração de Azure Monitor. Para obter mais informações, consulte [transmitir dados de monitoramento do Azure para um hub de eventos](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
Os recursos no Azure podem ter suas informações de diagnóstico enviadas diretamente para um espaço de trabalho Log Analytics onde consultas complexas podem ser executadas em relação às informações para solução de problemas e análise.  Para obter mais informações, consulte [coletar logs de recursos do Azure no espaço de trabalho log Analytics no Azure monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

## <a name="limitations"></a>Limitações
Atualmente, há as seguintes limitações ao usar o recurso de exportação de métricas para logs para balanceadores de carga:
* As métricas são atualmente exibidas usando nomes internos quando exportadas como logs você pode encontrar o mapeamento na tabela abaixo
* A dimensionalidade das métricas não é preservada. Por exemplo, com métricas como DipAvailability (status de investigação de integridade), você não poderá dividir ou exibir por endereço IP de back-end
* As portas SNAT usadas e as métricas de portas SNAT alocadas não estão disponíveis no momento para exportação como logs

## <a name="next-steps"></a>Próximas etapas
* [Examine as métricas disponíveis para seu Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [Criar e testar consultas seguindo instruções de Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
* Forneça comentários sobre este artigo ou Load Balancer funcionalidade usando os links abaixo
