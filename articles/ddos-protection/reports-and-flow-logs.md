---
title: Relatórios padrão da proteção contra DDoS do Azure e logs de fluxo
description: Saiba como configurar relatórios e logs de fluxo.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 2c4349662b06ff281ef028c833c6c43dd2707051
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905040"
---
# <a name="configure-ddos-attack-mitigation-reports-and-flow-logs"></a>Configurar relatórios de mitigação de ataque de DDoS e logs de fluxo 

O padrão de Proteção contra DDoS do Azure fornece informações detalhadas de ataque e visualização com Análise de Ataque de DDoS. Os clientes que protegem suas redes virtuais contra ataques de DDoS têm visibilidade detalhada sobre o tráfego de ataque e as ações tomadas para reduzir o ataque por meio de relatórios de mitigação de ataque e logs de fluxo de mitigação. A telemetria avançada é exposta por meio de Azure Monitor incluindo métricas detalhadas durante a duração de um ataque de DDoS. Alertas podem ser configurados para qualquer métrica do Azure Monitor exposta pela Proteção contra DDoS. O registro em log pode ser integrado com o [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection), Splunk (hubs de eventos do Azure), OMS log Analytics e armazenamento do Azure para análise avançada por meio da interface de diagnóstico de Azure monitor.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Exibir e configurar relatórios de mitigação de ataque de DDoS
> * Exibir e configurar logs de fluxo de mitigação de ataque de DDoS

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Antes de concluir as etapas deste tutorial, você deve primeiro criar um plano de [proteção padrão do DDoS do Azure](manage-ddos-protection.md).

## <a name="view-and-configure-ddos-attack-mitigation-reports"></a>Exibir e configurar relatórios de mitigação de ataque de DDoS

Relatórios de mitigação de ataque usam os dados do protocolo Netflow agregados para fornecer informações detalhadas sobre o ataque em seu recurso. Sempre que um recurso IP público estiver sob ataque, a geração de relatórios começará assim que a atenuação for iniciada. Também será gerado um relatório de incremental a cada 5 minutos e um relatório de pós-atenuação para todo o período de mitigação. Isso é para garantir que, no caso de o ataque de DDoS continuar por mais tempo, você poderá exibir o instantâneo mais recentes do relatório de atenuação a cada 5 minutos e um resumo completo quando a mitigação de ataque estiver concluída. 

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro** . Selecione **Monitorar** quando aparecer nos resultados.
3. Em **Configurações** , selecione **Configurações de diagnóstico** .
4. Selecione a **Assinatura** e o **Grupo de recursos** que contêm o endereço IP público que você deseja registrar.
5. Selecione **Endereço IP público** para **Tipo de recurso** , em seguida, selecione o endereço IP público específico para o qual você deseja registrar métricas.
6. Selecione **Ativar diagnóstico para coletar o log DDoSMitigationReports** e, em seguida, selecione tantas opções a seguir quanto você precisar:

    - **Arquivar em uma conta de armazenamento** : os dados são gravados em uma conta de armazenamento do Azure. Para saber mais sobre essa opção, consulte [arquivar logs de recursos](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Transmitir para um hub de eventos** : permite que um destinatário de log colete os logs usando um Hub de Eventos do Azure. Os Hubs de Eventos habilitam a integração com o Splunk ou outros sistemas SIEM. Para saber mais sobre essa opção, consulte [transmitir logs de recursos para um hub de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Enviar para log Analytics** : grava logs no serviço Azure monitor. Para saber mais sobre essa opção, consulte [coletar logs para uso em logs de Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Os relatórios de mitigação incrementais e pós-ataque incluem os seguintes campos
- Vetores de ataque
- Estatísticas de tráfego
- Motivo para pacotes ignorados
- Protocolos envolvidos
- Principais 10 regiões ou países de origem
- Principais 10 ASNs de origem

## <a name="view-and-configure-ddos-attack-mitigation-flow-logs"></a>Exibir e configurar logs de fluxo de mitigação de ataque de DDoS
Os logs de fluxo de mitigação de ataque permitem que você examine o tráfego descartado, o tráfego encaminhado e outros pontos de extremidade interessantes durante um ataque de DDoS ativo quase em tempo real. Você pode ingerir o fluxo constante desses dados no Azure Sentinel ou em seus sistemas SIEM de terceiros por meio do hub de eventos para monitoramento quase em tempo real, tomar ações em potencial e atender à necessidade de suas operações de defesa.

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro** . Selecione **Monitorar** quando aparecer nos resultados.
3. Em **Configurações** , selecione **Configurações de diagnóstico** .
4. Selecione a **Assinatura** e o **Grupo de recursos** que contêm o endereço IP público que você deseja registrar.
5. Selecione **Endereço IP público** para **Tipo de recurso** , em seguida, selecione o endereço IP público específico para o qual você deseja registrar métricas.
6. Selecione **Ativar diagnóstico para coletar o log DDoSMitigationFlowLogs** e, em seguida, selecione tantas opções a seguir quanto você precisar:

    - **Arquivar em uma conta de armazenamento** : os dados são gravados em uma conta de armazenamento do Azure. Para saber mais sobre essa opção, consulte [arquivar logs de recursos](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Transmitir para um hub de eventos** : permite que um destinatário de log colete os logs usando um Hub de Eventos do Azure. Os Hubs de Eventos habilitam a integração com o Splunk ou outros sistemas SIEM. Para saber mais sobre essa opção, consulte [transmitir logs de recursos para um hub de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Enviar para log Analytics** : grava logs no serviço Azure monitor. Para saber mais sobre essa opção, consulte [coletar logs para uso em logs de Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="azure-sentinel-data-connector"></a>Conector de dados do Azure Sentinel

Você pode conectar seu ao Azure Sentinel, exibir e analisar seus dados em pastas de trabalho, criar alertas personalizados e incorporá-los em processos de investigação. Para se conectar ao Azure Sentinel, confira [conectar-se ao Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection). 

![Conector de DDoS do Azure Sentinel](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Pasta de trabalho de proteção contra DDoS do Azure

Para exibir os dados de logs de fluxo no painel de análise do Azure, você pode importar o painel de exemplo do https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook

Os logs de fluxo terão os seguintes campos: 
- IP de origem
- IP de destino
- Porta de origem 
- Porta de destino 
- Tipo de protocolo 
- Ação executada durante a mitigação

![Pasta de trabalho proteção contra DDoS](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

A análise de ataque só funcionará se a proteção contra DDoS Standard estiver habilitada na rede virtual do endereço IP público. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

- Exibir e configurar relatórios de mitigação de ataque de DDoS
- Exibir e configurar logs de fluxo de mitigação de ataque de DDoS

Para saber como testar e simular um ataque de DDoS, consulte o guia de testes de simulação:

> [!div class="nextstepaction"]
> [Testar por meio de simulações](test-through-simulations.md)

