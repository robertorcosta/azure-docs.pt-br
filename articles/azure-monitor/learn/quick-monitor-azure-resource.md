---
title: Coletar dados de uma máquina virtual do Azure com o Azure Monitor | Microsoft Docs
description: Saiba como habilitar a Extensão de VM do agente do Log Analytics e como habilitar a coleta de dados das VMs do Azure com o Log Analytics.
ms.service: azure-monitor
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 85c953c4acdc31cc6d79600951ba745346771b0c
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75542222"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Início Rápido: Monitorar um recurso do Azure com o Azure Monitor
O [Azure Monitor](../overview.md) inicia a coleta de dados dos recursos do Azure no momento em que eles são criados. Este guia de início rápido apresenta uma breve explicação dos dados coletados automaticamente para um recurso e como exibi-los no portal do Azure para um recurso específico. Posteriormente, você pode adicionar a configuração para coletar dados adicionais e acessar o menu de Azure Monitor para usar as mesmas ferramentas para acessar os dados coletados para todos os recursos em sua assinatura.

Para obter descrições mais detalhadas dos dados de monitoramento coletados dos recursos do Azure, confira [Como monitorar recursos do Azure com o Azure Monitor](../insights/monitor-azure-resource.md).


## <a name="sign-in-to-azure-portal"></a>Entre no portal do Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 


## <a name="overview-page"></a>Página de visão geral
Muitos serviços incluirão dados de monitoramento na página de **Visão Geral** como uma visualização rápida da operação. Normalmente, isso será baseado em um subconjunto de métricas de plataforma armazenado nas Métricas do Azure Monitor.

1. Localize um recurso do Azure em sua assinatura.
2. Acesse a página **Visão Geral** e observe se há dados de desempenho exibidos. Esses dados serão fornecidos pelo Azure Monitor. O exemplo a seguir é a página **Visão Geral** para uma conta de armazenamento do Azure e você pode ver que são exibidas várias métricas.

    ![Página de visão geral](media/quick-monitor-azure-resource/overview.png)

3. Você pode clicar em qualquer um dos grafos para abrir os dados no Metrics Explorer, que é descrito abaixo.

## <a name="view-the-activity-log"></a>Exibir o log de Atividades
O log de atividades fornece informações sobre as operações em cada recurso do Azure na assinatura. Isso incluirá informações como quando um recurso é criado ou modificado, quando um trabalho é iniciado ou quando ocorre uma determinada operação.

1. Na parte superior do menu do recurso, selecione **Log de atividades**.
2. O filtro atual é definido como eventos relacionados ao seu recurso. Se você não vir nenhum evento, tente alterar o **Período** para aumentar o escopo de tempo.

    ![Log de atividades](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Se você quiser ver eventos de outros recursos em sua assinatura, altere os critérios no filtro ou até mesmo remova as propriedades do filtro.

    ![Log de atividades](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Métricas de exibição
Métricas são valores numéricos que descrevem algum aspecto de seu recurso em um momento específico. O Azure Monitor coleta automaticamente as métricas de plataforma a intervalos de um minuto de todos os recursos do Azure. Você pode exibir essas métricas usando o Metrics Explorer.

1. Na seção **Monitoramento** do menu do recurso, selecione **Métricas**. Isso abre o Metrics Explorer com o escopo definido para seu recurso.
2. Agora, clique em **Adicionar métrica** para adicionar uma métrica ao gráfico.
   
   ![Metrics Explorer](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Selecione uma **Métrica** na lista suspensa e, em seguida, uma **Agregação**. Isso define como os valores coletados serão amostrados em cada intervalo de tempo.

    ![Metrics Explorer](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Clique em **Adicionar métrica** para adicionar outras combinações de métrica e agregação ao gráfico.

    ![Metrics Explorer](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você exibiu o log de atividades e as métricas de um recurso do Azure coletados automaticamente pelo Azure Monitor. Os logs de recursos fornecem informações sobre a operação detalhada do recurso, mas devem ser configurados para que sejam coletados. Continue no tutorial para coletar logs de recursos em um workspace do Log Analytics em que eles podem ser analisados usando consultas de log.

> [!div class="nextstepaction"]
> [Coletar e analisar logs de recursos com o Azure Monitor](tutorial-resource-logs.md)
