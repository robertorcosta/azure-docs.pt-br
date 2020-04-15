---
title: Configure alertas de disponibilidade com o Azure Application Insights | Microsoft Docs
description: Configure testes da web no Application Insights. Obtenha alertas se um site fica indisponível ou responde lentamente.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 5af6aec2267384c37f664522d075bf26c632e7e9
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382877"
---
# <a name="availability-alerts"></a>Alertas de disponibilidade

[Application Insights do Azure](../../azure-monitor/app/app-insights-overview.md) envia solicitações da Web ao aplicativo em intervalos regulares de pontos no mundo todo. Ele pode alertá-lo se sua aplicação não está respondendo, ou se ele responde muito lentamente.

## <a name="enable-alerts"></a>Habilitar alertas

Os alertas agora são ativados automaticamente por padrão, mas para configurar totalmente o alerta, primeiro você precisa criar seu teste de disponibilidade.

![Criar experiência](./media/availability-alerts/create-test.png)

> [!NOTE]
>  com os [novos alertas unificados](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), as preferências de notificação e a gravidade de regra de alerta com [grupos de ação](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **deve ser** configurada no experiência de alertas. Sem as etapas a seguir, você só receberá notificações no portal.

1. Depois de salvar o teste de disponibilidade, na guia Detalhes, clique nas reticências do teste que acabou de criar. Clique em "Editar alerta".

   ![Editar depois de salvar](./media/availability-alerts/edit-alert.png)

2. Defina o nível de severidade desejados, a descrição da regra e mais importante, o grupo de ação que tem as preferências de notificação que você deseja usar para essa regra de alerta.

   ![Editar depois de salvar](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> Os alertas de disponibilidade criados através dessa experiência são baseados no estado. Isso significa que quando os critérios de alerta são atendidos, um único alerta é gerado quando o site é detectado como indisponível. Se o site ainda estiver abaixo na próxima vez que os critérios de alerta forem avaliados, isso não gerará um novo alerta. Então, se o seu site estivesse desligado por uma hora e você tivesse configurado um alerta de e-mail, você só receberia um e-mail quando o site fosse derrubado, e um e-mail subseqüente quando o site voltasse. Você não receberia alertas contínuos lembrando que o site ainda não estava disponível.

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Alertar sobre X fora de locais de Y relatando falhas

O X fora de locais de Y regra de alerta está habilitada por padrão na [experiência de novos alertas unificados](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), quando você cria um novo teste de disponibilidade. Você pode recusá-la selecionando a opção "clássica" ou optar por desabilitar a regra de alerta.

> [!NOTE]
> Configure os grupos de ação para receber notificações quando o alerta for disparado, seguindo as etapas acima. Sem essa etapa, você só receberá notificações no portal quando a regra dispara.
>

### <a name="alert-on-availability-metrics"></a>Alertar sobre métricas de disponibilidade

Usando os [novos alertas unificadas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), você pode alertar sobre a disponibilidade de agregação segmentada e métricas de duração de teste:

1. Selecione um recurso do Application Insights na experiência de métricas e selecione uma métrica de disponibilidade:

    ![Seleção de métricas de disponibilidade](./media/availability-alerts/select-metric.png)

2. Configure alertas de opção do menu levara você para a nova experiência de onde você pode selecionar testes específicos ou locais para configurar a regra de alerta no. Você também pode configurar os grupos de ação para esta regra de alerta aqui.

### <a name="alert-on-custom-analytics-queries"></a>Alertar sobre consultas de análises personalizadas

Usando [novos alertas unificados](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), você pode alertar sobre [consultas de log personalizado](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Com consultas personalizadas, você pode alertar sobre qualquer critério arbitrário que ajuda você a obter o sinal de mais confiável dos problemas de disponibilidade. Isso também é aplicável, se você estiver enviando resultados de disponibilidade personalizados usando o TrackAvailability SDK.

> [!Tip]
> As métricas sobre dados de disponibilidade incluem resultados disponibilidade personalizado que você pode estar enviando chamando nosso SDK TrackAvailability. Você pode usar os alertas de suporte a métricas para alertar sobre resultados de disponibilidade personalizado.
>

## <a name="automate-alerts"></a>Automatize alertas

Para automatizar esse processo com os modelos do Azure Resource Manager, consulte o Criar um alerta métrico com a documentação [do modelo do Resource Manager.](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert)

## <a name="troubleshooting"></a>Solução de problemas

Artigo dedicado [para solução de problemas](troubleshoot-availability.md).

## <a name="next-steps"></a>Próximas etapas

* [Testes na Web com diversas etapas](availability-multistep.md)
* [Testes web de ping de URL](monitor-web-app-availability.md)
